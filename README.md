# CV Generator
<br>

## Hakkında
Bu proje; Linux Kabuk Programlama, HTML ve CSS teknolojileri kullanılarak Linux Araçları ve Kabuk Programlama dersi ödevi için hazırlanmış özelleştirilebilir CV üretmeyi sağlayan bir uygulamadır. **Projeyi anlattığım YouTube adresine şu bağlantı aracılığıyla erişebilirsiniz: http://com/**

## Kullanılan Teknolojiler
##### -HTML
##### -CSS
##### -Linux Kabuk Programlama

## Nasıl Çalıştırılır?
Linux işletim sistemli ve Zenity kurulu bir cihaz üzerinde projenin buluduğu dizinde Komut Satırı açıp ardından şu kodla çalıştırınız;
```
bash cvgenerator
```

## Programın Tanıtılması
Program; üretilen her CV'yi ayrı bir klasör içinde barındırmakta ve her CV için ayrı style/html dosyaları oluşturmaktadır. Bu sayede önceden üretilen bir CV silinmeden yeni bir CV üretmeye imkan verilmektedir.
```
# DOSYA ADINI BELİRLEME İŞLEMLERİ BAŞLANGICI
sayac=0
while [ true ]
do
  folder_name="cv$sayac"
  if [ -e "./$folder_name" ]
  then
    sayac=`expr $sayac + 1`
  else
    FOLDER="$folder_name"
    break
  fi
done

# DOSYA ADINI BELİRLEME İŞLEMLERİ BİTİŞİ
```

Program açıldıktan sonra ilk aşamada CV sahibinin isminin, fotoğrafının üstünde mi altında mı görünmesine karar vermesini istemektedir. Burada yapılacak seçim, ilgili kısmı aşağıdaki gibi gösterecektir;

##### İSİM RESMİN ÜSTÜNE
![Screenshot](https://github.com/basturkerhan/walls-and-stairs-2d-game/blob/main/readme-images/0.png)

##### İSİM RESMİN ALTINA
![Screenshot](https://github.com/basturkerhan/walls-and-stairs-2d-game/blob/main/readme-images/0.png)

Ayrıca seçilen profil fotoğrafı kaydedilirken sadece uzantısı JPG/PNG olan yüklemeler kabul edilmektedir. Bunu kontrol eden kod aşağıdadır;
```
0)
       IFS="/" read -a name <<< $PHOTOINPUT

       isim=${name[-1]}

       if [ `echo "$isim" | grep .jpg$` ]
       then
         cp "$PHOTOINPUT" ./resimler/
         HTML="$HTML <br><img class=\"user-image img img-fluid\" src=\"./resimler/$isim\"><br>"
         return 1
       elif [ `echo "$isim" | grep .png$` ]
       then
         echo "Lütfen sadece png veya jpg formatta resim seciniz"
         return 0
       else
         echo "Lütfen sadece png veya jpg formatta resim seciniz"
         return 0
       fi

    ;;
```

Ayrıca örnek olması açısından kullanıcı deneyimlerini alıp HTML'ye basan kod da aşağıda verilmiştir;
```
# KULLANICININ DENEYİMLERİNİ ALMA FONKSİYONU BAŞLANGICI
get_user_experience() {
  EXINPUT=`zenity --entry --title="Deneyimler" --text="Aralarında virgül olacak şekilde deneyimlerinizi giriniz..."`

  case $? in
    0)
      ex_html="
            <!-- DENEYİMLER ALANI -->
            <div class=\"row section\">
            <div class=\"col-md-6\">
                <div class=\"card shadow\">
                    <div class=\"card-header\"><h2>Deneyimler</h2></div>
                    <div class=\"card-body\">
                        <ul>"

      IFS=","
      read -a exs <<< $EXINPUT

      for experience in ${exs[*]}
      do
        ex_html="$ex_html <li>$experience</li>"
      done
      HTML="$HTML $ex_html </ul></div></div></div>"
      get_user_volunteer_experience
      ;;
    1)
	  echo "Program CV oluşturmadan sonlandırıldı";;
   -1)
	  echo "Program CV oluşturmadan sonlandırıldı";;
  esac
}
# KULLANICININ DENEYİMLERİNİ ALMA FONKSİYONU BİTİŞİ
```

Daha sonra CV sahibinin bilgilerini alacak ekranlar gelmeye başlayacaktır. Burada;
##### - İsim Soyisim, Github, Telefon Numarası, Email Adresi, Linkedin Adresi
##### - Fotoğraf
##### - Hakkında
##### - Deneyimler
##### - Gönüllü Deneyimler
##### - Yetenekler
##### - Eğitim Alınan Yerler

Gibi bilgilerin girilmesi beklenmektedir.

Daha sonra; özelleştirilmemiş CV'nin şablonunu gösteren bir ekran açılacaktır. Devam edildiğinde CV HTML belgesini özelleştirmeyi sağlayan aşamalara gidilecektir.
Burada;
##### - Temaya ait arkaplan renkleri
##### - Kart renkleri
##### - İçerik renkleri
##### - Başlıkların renkleri
##### - Ana yazıların renkleri
##### - Ara çizgi renkleri
##### - (...)

Gibi özelleştirme seçenekleri mevcuttur. Yapılan her seçim "custom.css" dosyasına farklı bir CSS kodu ekleyerek ana belgenin özelleştirilebilmesini sağlar.

Örnek bir özelleştirme kodu aşağıda verilmiştir;
```
select_education_card_background() {
  EABGCOLOR=`zenity --title="Eğitim Kartlarının Arkaplan Rengi" --text="Eğitim kartlarının arkaplan rengini seçiniz..." --color-selection --show-palette`

  case $? in
    0)
      CUSTOMCSS="$CUSTOMCSS .education-card {background-color: $EABGCOLOR;} "
      cv_cikart ;;
    1)
      echo "Renk Seçmedin" ;;
   -1)
      echo "Renk Seçmedin" ;;
  esac
}
```

Tüm seçim işlemleri bitmeden yeni bir HTML CV dökümanı oluşmayacaktır. Belgeyi oluşturma ve kaydetme işlemleri son adımdaki şu kodlar aracılığıyla yapılmaktadır.
```
cv_cikart() {
    mkdir "$FOLDER"
    mkdir "$FOLDER/css"
    cp ./css/* "$FOLDER/css"
    mkdir "$FOLDER/resimler"
    cp ./resimler/* "$FOLDER/resimler"

    cat sablon.html > "$FOLDER/index.html"
    echo "$HTML <footer><div class="card"><div class="card-body"><h2>$FULLNAME</h2></div></div></footer></body></html>" >> "$FOLDER/index.html"
    echo "$CUSTOMCSS" >> "$FOLDER/css/custom.css"
    # firefox....
}
```

## Programın İçinden Resimler


## Bazı Örnek CV HTML Belgeleri
