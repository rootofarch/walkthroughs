#   DonkeyDocker

Selam,
Finallerimiz bitince 4 arkadaş herkes elinden geldiğince vmler çözelim ve yayınlayalım dedik,
süre belirledik ve çözmeyen kişi veya kişiler bu ruleti ana sisteminde yedek almadan oynayacak diye karar aldık

![screenshot](screenshots/ScriptFoalsRulet.png)

Bu vesileyle DonkeyDocker sanal makinasının tam çözümünü yaptık, sizlerle paylaşmak istedik. Keyifli okumalar.
VM hakkında ayrıntılı bilgi alabileceğiniz ve indirme bağlantısını bulabileceğiniz link aşağıdadır

[VulnHub](https://www.vulnhub.com/entry/donkeydocker-1,189/)

#   VM çözümü

İlk önce hedef sistemi bulmak için

    arp -a

komutu ile **arp** tablosunu edinip hedefimizi **192.168.2.8* **IP adresi** nde buluyoruz

    nmap -n -p 1-65535 -sV 192.168.2.8 --open
    
ile tüm açık tcp portlarını listeliyoruz ve sadece **http(80)** ve **ssh(22)** portu açık görüyoruz

![screenshot](screenshots/01.png)

bunun üzerine HTTP portuna yönelip browserımızda

    192.168.2.8
    
adresine gitmeyi deniyoruz.

![screenshot](screenshots/02.png)

Siteye girdiğimizde bizi vm hakkında ve hazırlayan hakkında
bilgilendiren bir sayfa karşılıyor ve sayfa kodlarında da birşey bulamayınca, 
hemen adresi *owasp zap* aracında tarattığımızda **robots.txt** yi görünce kalbimiz çarpsa
da içinden birşey çıkmıyor. **Dirb**den yardım dilenerek 

![screenshot](screenshots/03.png)

birkaç ek sayfa daha buluyoruz tabi bulduklarımıza gözatmayı ihmal etmiyoruz
*http://192.168.2.8/mailer/examples/index.html* sayfasını ziyaret ediyoruz ve 
**phpmailer** anahtar sözcüğünü biyere not ediyoruz.

![screenshot](screenshots/04.png)

Sistem hakkında bilgiler toplamak için

    curl -I http://192.168.2.8/

komutunu kullansakta yalnızca **apache** versiyonunu öğrenebiliyoruz.

![screenshot](screenshots/05.png)

Elde edilen bilgiler bir ipucuna götürmediği için yönümüzü **exploit** aramaya çeviriyoruz.
**Apache** versiyonundan birşey yakalayamıyoruz ancak, **phpmailer** ilgimizi çekmeye başlıyor.

![screenshot](screenshots/06.png)

Vm çözmenin %90 ı okumaktır deyip *nasıl öğrenirim ben bu versiyonu?* sorusuyla google a gidiyoruz.

![screenshot](screenshots/07-1.png)

![screenshot](screenshots/07-2.png)

**VERSION** dosyasında yazdığını öğrenip *acaba mı?* diyerek bir elimiz kalbimizde bakıyoruz.

![screenshot](screenshots/07-3.png)

versiyonu görünce hemen download arıyor gözlerimiz **exploitdb**de 
pythona ihanet olmaz diyerek **PHPMailer < 5.2.18 Remote Code Execution (Python)** 
yazısına 3-5-10 kere tıklıyoruz ve indiriyoruz

![screenshot](screenshots/08.png)

**vim** ile kendimize göre düzenleyip (anarcoder fantezi yazısını silmeniz veya dosyanın en başına

    # -*- coding: utf-8 -*-
    
yazmanız gerekmekte) kaydedip çalıştıramıyoruz çünkü eksik kütüphanemiz varmış, onu da

    pip2 install requests_toolbelt
    
ile indirip

    nc -lvp 8080

ile **8080** portumuzu dinlerken exploitimizi

    python 40974.py

ile çalıştırıyoruz.

![screenshot](screenshots/09.png)

uzun sayılabilecek bir süre bekledikten sonra işlemimiz bitiyor.

![screenshot](screenshots/10.png)

*reverse shell* imiz düşmezse **nc** dinlemeye devam ederken firefoxdan

    http://192.168.2.8/backdoor.php
    
sayfasını ziyaret etmeniz yeterli olacaktır.

![screenshot](screenshots/11.png)

biraz genel geçer arama yapıyoruz smith kullanıcı adını öğreniyoruz fakat bir açık göremeyince

![screenshot](screenshots/12.png)

yok artık exploit sonrası *ssh bruteforce* mu? dememize kalmadan

![screenshot](screenshots/13.png)

publickey veremediğimiz için onuda yapamıyoruz ve tekrar aramaya başlıyoruz.

![screenshot](screenshots/grumpycat.png)


    su smith
    
ile geçiş yapmayı denesekte gerçek bir shell olmadığı için hata alıp elimiz kolumuz bağlı oturuyoruz
*shell spawning* yollarına bakıp python yine gözükünce hemen yapıştırıyoruz.

![screenshot](screenshots/14.png)

ve **su smith** denememizi yapıyoruz nitekim *shell* konusunda sorun çıkmıyo ama **parola?**
*qwe123* ler *password* ler havada uçuşurken **GameOfPwners** ta ipucu olarak *ön tanımlı parola*
denilen *kullanıcı adı* geliyo aklımıza ve parolaya da **smith** yazıyoruz.

![screenshot](screenshots/15.png)


![screenshot](screenshots/OMG.png)

eşimizi dostumuzu ekran başına çağırıp göstermeden devam etmeyelim lütfen... 
arıyoruz tarıyoruz fakat yine kitlenince *flag* daki *PS* (not) dikkatimizi çekiyor ve **grep** ten zarar gelmez diyip

    grep -ar "1984"

    
    grep -ari "george"
    
    
    grep -ari "orwell"

    
![screenshot](screenshots/16.png)

denediğimize değdiğini görüyoruz. Tabi durmayıp dizine gidiyoruz.

![screenshot](screenshots/17.png)

dizinin komple bir link olduğunu (**S**) ve *orwell* isimli bir kullanıcıya *parolasız* bağlanılması için **key** kaydı yapıldığını görüyoruz
nasıl bunu kullanırım sorusunun cevabını ssh manualinde arayıp bulduktan sonra

![screenshot](screenshots/18.png)

id_ed25519 ve id_ed25519.pub dosyalarını kalimizde aynen oluşturup(içeriklerini kopyalayıp) acımadan kullanıyoruz

![screenshot](screenshots/19.png)

#İÇERDEMA

![screenshot](screenshots/20.png)


![screenshot](screenshots/ibo.png)

ve tabi bi flagınızı alırız diyerek

![screenshot](screenshots/21.png)

yine aramalarımıza hız vererek (güya daha deneyimliyiz ya)

![screenshot](screenshots/22.png)


![screenshot](screenshots/23.png)

ilginç birşeyle karşılaşıyoruz.Şahsen benim için ilk kez karşılaştığımdan dolayı ilginç
"nedir bu busybox?" diye ararken androidleri root etmede kullanıldığını genel linux uygulamalarını içerdiğini öğreniyoruz.
ve **Shell** imizin **ash(Almquist shell)** olduğunu ayrıca **orwell** imizin docker gurubuna dahil olduğunu öğreniyoruz.
Dolayısıyla dockerında manuelini okumaya ve internetten araştırmaya başlıyoruz.

![screenshot](screenshots/24.png)


![screenshot](screenshots/25.png)


    docker container ls
    
ile containerleri listeliyoruz ve sadece **donkeydocker** adlı bir container olduğunu görüyoruz.
Bu containerın neler çalıştırdığına göz atarak *main.sh* ı görüyoruz

![screenshot](screenshots/26.png)

dolayısıyla ona ulaşmaya çalışarak komutlarımızı

    docker container exec donkeydocker
    
ın sonuna ekliyoruz. Fakat containerın ayaklanmasında kullanılan komutlar olduğunu görüp

![screenshot](screenshots/27.png)

zaten containerda **root** muşuz napalım main.sh ı diyerek umursamıyoruz

![screenshot](screenshots/28.png)

hatta birara tembellik bayrağını elimize alıp taşıyıp bıraktığımız bile oluyor

![screenshot](screenshots/29.png)

flag arıyoruz ama flag containerda olmadığından dolayı bulamıyoruz

![screenshot](screenshots/30.png)

burası beni en çok yoran kısım oldu çünkü birşeyi öğren uygula ve hatta kötüye kullan **okadar da hızlı** yapılamıyor

![screenshot](screenshots/cahil.png)

Docker grubunun root grubuna eşdeğer olduğunu dolayısıyla atlatılabildiğini [şu abimizden](https://reventlov.com/advisories/using-the-docker-command-to-root-the-host) öğrendim.
Tabi basit değişiklikler yaparak uyguladım (debian yerine ubuntu ve **aucc** ler)
Bir klasör oluşturup içerisine Dockerfile adında bir dosyaya

    FROM ubuntu:14.04
    ENV WORKDIR /aucc
    RUN mkdir -p $WORKDIR
    VOLUME [ $WORKDIR ]
    WORKDIR $WORKDIR

yazıyoruz ve 

    docker build -t aucc-docker-image .

ile build ediyoruz
    
![screenshot](screenshots/31.png)


![screenshot](screenshots/32.png)


    docker run -v /root:/aucc -t aucc-docker-image /bin/bash -c 'ls -l'
    
ile dosya ve dizinleri listeleyip

    docker run -v /root:/aucc -t aucc-docker-image /bin/bash -c 'cat flag.txt'
    
ile flagımızı görüp şaşı bakan gözlerimizi dinlendiriyoruz

![screenshot](screenshots/33.png)


![screenshot](screenshots/devamedebilirim.png)
