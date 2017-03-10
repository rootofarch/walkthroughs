#   Wallaby's: Nightmare (v1.0.2)

Selam,
Wallaby's: Nightmare (v1.0.2) sanal makinanın tam çözümünü yaptık, sizlerle paylaşmak istedik. Keyifli okumalar.

VM hakkında ayrıntılı bilgi alabileceğiniz ve indirme bağlantısını bulabileceğiniz link aşağıdadır

[VulnHub](https://www.vulnhub.com/entry/wallabys-nightmare-v102,176/)

#   VM çözümü

İşe herzamanki gibi maksimum özgüvenle hedef tespiti ve port tarama ile başlıyoruz.

![screenshot](screenshots/01.png)

ve hedef sistemimizin **192.168.1.22** IP adresinde ve **ssh(22) , http(80) TCP** portlarının açık ve **irc(6667)** portununda filtreli bir şekilde hizmet verdiğini görüyoruz. **IRC** ile uğraşmak bana göre bir dert olduğu için hiç denemeden **http**ye yöneliyoruz. Default portunda hizmet verdiği için port belirtmeden, doğrudan ip adresini yazarak bağlanabiliyoruz ve bir kullanıcı adı ile üye oluyoruz (**aucc**). Bizi, başımıza geliceklerden haberdar eden şu ekran karşılıyor.

![screenshot](screenshots/02.png)

**Start the CTF!* diyerek insanlık için küçük akıl sağlığımız için büyük bir adım atıyoruz.

![screenshot](screenshots/03.png)

gelen sayfa gözümüzü korkutmuyor bile yukardaki **?page=** i farkedince "Hadi canım!" diyip **passwd** dosyasını görmeye çalışıyoruz.

![screenshot](screenshots/04.png)

bikaç deneme daha yapalım derken ensemizden bizi yakaladığı gibi "Nereye? Kolay mı geldi?" diye bir yazı gösteriyor.

![screenshot](screenshots/05.png)

başa dönelim demeye kalmadan sayfanın yerinde olmadığını görüyoruz.

![screenshot](screenshots/06.png)

hemen panikle **nmap**in kapısına tekrar gelip.

![screenshot](screenshots/07.png)

**60080 TCP** portunda **http**yi görünce

![screenshot](screenshots/malkocoglu_firar.png)

gibi bir sitemle tarayıcımıza tekrar dönüyoruz.

![screenshot](screenshots/08.png)

Sayfamız değişmiş ama sorgu hala çalışıyormu diye denememizle "bumuydu güvenlik" dememiz bir oluyor.

![screenshot](screenshots/09.png)

ilk sayfada neden **"Fuzzing is your friend."** dediğiyle ilgili kafamızda şimşekler çakıp,

    dirb http://192.168.1.22:60080/?page= /usr/share/dirb/wordlists/common.txt

ile sayfaya hazır bir wordlist ile dictionary attack başlatıyoruz

![screenshot](screenshots/10.png)

bu çıkan adresleri denediğimizde **?page=index**te ilk kayıt olduğumuz sayfayı, **?page=home**da da varsayılan olarak açılan sayfayı görüyoruz. **?page=.git/HEAD**'den birşey çıkıcak diye ümitlensekde bu ümidimiz çok sürmüyor. **?page=contact**'ta önemli birşeyler bulamayıp **?page=mailer** da ipucunu alıyoruz.

![screenshot](screenshots/11.png)

ipucundan hareketle, adresimizi komut verebilecek hale getiriyoruz.

![screenshot](screenshots/12.png)

![screenshot](screenshots/13.png)

hatta bir ara abartıp:

![screenshot](screenshots/14.png)

kendimize güldürtüp, demek başkalarıda deniyormuşki bunu yazmışlar diyerek teselli bulmaya çalışıyoruz. O depresyonla ircye razı olsak bile **Irssi** 'den
    
    irssi -c 192.168.1.22 -p 6667
    
ile bağlanmayı denediğimizde **Connection Timed Out** hatasıyla o bize razı olmuyor. Damarımıza bastıklarından olsa gerek [pentestmonkey](http://pentestmonkey.net/)'den reverse shell beğenip python'ı görünce

![screenshot](screenshots/15.png)

kaliden
    
    nc -lvp 1234

ile **1234** portunu dinlerken, kodu kali makinemizin IP adresine göre düzenleyip acımadan kullanıyoruz ve bilgi toplamaya başlıyoruz.

![screenshot](screenshots/16.png)

![screenshot](screenshots/17.png)

yine bir zaafiyetli makine olduğu için tüm kullanıcılara verilmiş iptables'ı yönetme iznini kullanarak **irc* ye dışarıdan yolladığımız paketlerin drop edildiğini görüyoruz ve drop eden bu kuralı siliyoruz.

![screenshot](screenshots/18.png)

    irssi -c 192.168.1.22 -p 6667

ile yeniden bağlanmayı denediğimizde bağlanıyoruz ve

    /list

ile kanalları listeleyip,

    /join #wallabyschat

ile wallabyschat kanalına katılıyoruz.

![screenshot](screenshots/19.png)

    .help

ile seçeneklerimizi listeleyip

![screenshot](screenshots/20.png)

bize özelden gönderdiğini söyleyince

    /window 3

ile yolladığı seçeneklere bakıyoruz

![screenshot](screenshots/21.png)

bunların hangisi bizim işimize yarar diye baktığımızda **run** dikkatimizi çekiyor.

    /window 2

ile yeniden kanala dönüp **run** diyerek çalıştırmak istediğimizde hiçbir tepki almıyoruz ve **/run** ile **.run** 'ı da deniyoruz. Yalnızca **.run** 'da bize **Waldo* olmadığımızı söylüyor. "Oluruz canım dertettiğin şeye bak" deyip

    /nick waldo

dediğimizde tepki dahi vermiyor çünkü zaten kanalda bir **waldo* var. Onu atmayı denediğimizde de yetkimizin olmadığını söylüyor.

![screenshot](screenshots/22.png)

![screenshot](screenshots/hiyar.png)

işler burada geriye dönmemiz gerektiğini söylüyormuş. yeniden **python reverse shell* 'i ile bağlandığımız terminale gelirsek ve

    sudo --list

yaparsak **vim* ile bir dosyanın **waldo* adına "parola sormadan" açılabildiğini görmüş oluruz:

![screenshot](screenshots/23.png)

ile vim editörü üzerinden **bash** 'e **sh** 'a kısacası yeniden shelle çıkabiliriz. Zaten herhangi bir metin editörünü, böylesine gerçek olmayan bir terminalde kullanmamız malesef mümkün değil. Ben bireysel seçimim olarak yeni bir sayfa açmayı tercih ederek yeni bir reverse shell ile başka porttan(**2017**) devam ediyorum.

![screenshot](screenshots/24.png)

ve şuan waldonun nasıl olupta irc kanalına bağlı olduğunu öğrenmek için

    who

ile login olmuş kullanıcıları ve neler yaptıklarını listeliyoruz.

    pkill tmux

ile de waldo hesabında bulunduğumuz için bu çalışan **tmux* işlemini durduruyorum.

![screenshot](screenshots/25.png)

**irssi* 'ye döndüğümde waldonun çıktığını belirtiyor.

    /nick waldo

ile nickname'imizi waldo yapıp **.run** 'ı çalıştırmaya çalıştığımızda beklediğimiz cevabı alıyoruz. Sürekli .run yazmamak için yine bir **reverse shell** ile mide bulandırarak bağlantımı busefer **1996** portundan sağlıyorum.

![screenshot](screenshots/26.png)

bütün bıkkınlığımızla wallaby kullanıcısında

    sudo --list

çıktısını gördüğümüzde sonda olduğumuzu anlıyoruz çünkü hiçbir sudo parolası istemiyor. En mantıklısını yapıp,

    sudo su -

ile root oluyoruz,

    find / -name "flag.txt"

ile flag'ımızı **/root** dizininde bulup **cat** ile bastırıyoruz.

![screenshot](screenshots/27.png)

#   FLAG

![screenshot](screenshots/flag.png)

#   BU CTFDEN ÇIKAN SONUÇ

![screenshot](screenshots/shell.png)
