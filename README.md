# CV Generator
<br>

## Hakkında
Bu proje; Linux Kabuk Programlama, HTML ve CSS teknolojileri kullanılarak Linux Araçları ve Kabuk Programlama dersi ödevi için hazırlanmış özelleştirilebilir CV üretmeyi sağlayan bir uygulamadır. **Projeyi anlattığım YouTube adresine şu bağlantı aracılığıyla erişebilirsiniz: http://com/**

## Önemli Not
Projeyi çalıştırabilmek için Zenity yüklü olan GUI destekli Linux işletim sistemli bir makineye ihtiyaç vardır.

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
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/dizin_yapisi.PNG)
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

![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/baslik_secim.PNG)

##### İSİM RESMİN ÜSTÜNE
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/baslik_ust.PNG)

##### İSİM RESMİN ALTINA
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/baslik_alt.PNG)

Ayrıca seçilen profil fotoğrafı kaydedilirken sadece uzantısı JPG/PNG olan yüklemeler kabul edilmektedir. Bunu kontrol eden kod aşağıdadır;
```
# KULLANICININ FOTOĞRAFINI ALMA FONKSİYONU BAŞLANGICI
get_user_photo() {
  PHOTOINPUT=`zenity --width 600 --height 600 --file-selection --title="JPG veya PNG bir fotoğraf seçiniz."`
  case $? in
    0)
       IFS="/" read -a name <<< $PHOTOINPUT

       isim=${name[-1]}

       if [ `echo "$isim" | grep .jpg$` ]
       then
         HTML="$HTML <br><img class=\"user-image img img-fluid\" src=\"./resimler/$isim\"><br>"
         return 1
       elif [ `echo "$isim" | grep .png$` ]
       then
         HTML="$HTML <br><img class=\"user-image img img-fluid\" src=\"./resimler/$isim\"><br>"
         return 1
       else
         echo "Lütfen sadece png veya jpg formatta resim seciniz"
         return 0
       fi

    ;;
    1)
	  return 0 ;;
   -1)
	  return 0 ;;
  esac

}
# KULLANICININ FOTOĞRAFINI ALMA FONKSİYONU BİTİŞİ
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
    cp "$PHOTOINPUT" "./$FOLDER/resimler/"

    cat sablon.html > "$FOLDER/index.html"
    echo "$HTML <footer><div class="card"><div class="card-body"><h2>$FULLNAME</h2></div></div></footer></body></html>" >> "$FOLDER/index.html"
    echo "$CUSTOMCSS" >> "$FOLDER/css/custom.css"
    zenity   --info --width 500  --height 500  --text  '<span foreground="green" font="26"><i><b>CVGenerator</b> Bilgilerinizi Başarıyla İşledi ve CV Oluşturdu!</i></span>'
}
```

## Programın İçinden Resimler
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/presim2.PNG)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/presim4.PNG)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/presim1.PNG)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/presim5.PNG)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/presim3.PNG)
<br>

## Bazı Örnek CV HTML Belgeleri
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/cv1.png)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/cv2.png)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/cv3.png)
<br>
![Screenshot](https://github.com/basturkerhan/cv-generator/blob/main/readme-images/cv4.png)
<br>
