# concordium
concordium kayıt olma
# Baker : semihysr
# Bakerid: 6094

Cüzdan Kurulumu

https://developers.concordium.com/en/testnet4/testnet/guides/get-the-app.html adresine girerek Mobil cihazınıza uygun uygulamayı indiriniz.Kurulum yaptığınız uygulamayı açarak
önce kimlik daha sonrada hesap açıyoruz.Açtığımız hesapta Request 100 Gtu tıklayarak hesabımıza GTU alıyoruz(Hesabımıza 2000 GTU veriliyor).Bu işlemler bittikten sonra
uygulamanın more kısmından exporta tıklayarak cüzdanımızı dışa aktarıyoruz.Aktarım sırasında şifre oluşturuyoruz bu şifreyi unutmayınız. Dışa aktardığımız bu cüzdanı baker ekleme
ve transfer işlemleri için kullanacağız.


Concordium Node Kurulumu

//User açıyoruz
sudo adduser concordium

//Usere yetki veriyoruz
sudo visudo
root    ALL=(ALL:ALL) ALL 
altına bu satırı ekle 
concordium ALL=(ALL:ALL) ALL
CTRL+O enter CTRL+X

//Usere yetki veriyoruz
sudo usermod -a -G sudo concordium

//Usere giriyoruz.
sudo su - concordium

//Repoları güncelliyoruz.
sudo apt update && sudo apt upgrade

//docker kurulumu
sudo apt install docker.io curl -y
sudo systemctl start docker
sudo systemctl enable docker

//Yetki veriyoruz
sudo usermod -aG docker $USER
newgrp docker



//Dosyaları indiriyoruz
wget https://client-distribution-testnet.concordium.com/concordium-software-linux_0.4.8-0.tar.gz

//Dosyaları çıkarıyoruz
tar -xvf concordium-software-linux_0.4.8-0.tar.gz

//Dosyaları olması gereken foldere taşıyoruz
mkdir Documents
mv concordium-software $HOME/Documents/concordium-software

//Taşıdığımız klasöre giriyoruz
cd $HOME/Documents/concordium-software

//Portları açıyoruz
sudo ufw allow 8888
sudo ufw allow 8082
sudo ufw allow 10000

./concordium-node çalıştırılıp node ismi girilerek senkronizasyon başlatılıyor.



Senkronizasyon bitince 2. bir Putty açarak

sudo su - concordium

cd $HOME/Documents/concordium-software

Cüzanı import etme ( home/concordium//Documents/concordium-software klasörüne telefondan export ettiğimiz dosyayı winscp ile yükleyelim. 
Sonra <path/to/exported/file> cüzdan dosya adını bakerAccount yerine ise hesap adınızı yazınız. Bu işlemde cüzdan şifrenizi kullacaksınız. )

./concordium-client config account import <path/to/exported/file> --name bakerAccount
 
Key dosyası oluşturma (<keys-file> isim ver)
./concordium-client baker generate-keys <keys-file>.json


Baker kaydı (<keys-file> yerine yukarıda isim verdiğimiz key adı , bakerAccount yerine hesap adımız , <amountToStake> stake miktarı giriniz ör:1000, 1200 . Bu işlemde cüzdan şifrenizi kullacaksınız  

./concordium-client baker add <keys-file>.json --sender bakerAccount --stake <amountToStake> --out ~/.local/share/concordium/baker-credentials.json

Bu işlemi yaptıktan sonra ./concordium-node-stop kodu ile durdurup ./concordium-node ile tekrar başlatmamız gerekiyor.
Başlattığımızda tekrar isim yazdığımız kısım gelecek burada direk enter yapalım. Y/N kısmı geldiğinde N yapıp devam edelim.


Log Alma 

//Dosyayı indiriyoruz
wget https://client-distribution-testnet.concordium.com/retrieve_minified_logs-linux-v1

//Dosyaya yazma yetkisi veriyoruz.
sudo chmod +x retrieve_minified_logs-linux-v1

//log çıktı alma işlemi başlatıyoruz. Bu biraz zaman alabilir.
./retrieve_minified_logs-linux-v1

log dosyasını WinSCP programı veya benzeri programlarla bağlanıp /home/concordium/Documents/concordium-software dosyayolundan indirebilirsiniz.Özet log olup görev bildirimlerinde kullanılır.



Rust Kurulumu


curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

source $HOME/.cargo/env

rustup target add wasm32-unknown-unknown

cd ~/Documents/concordium-software/

cp cargo-concordium $HOME/.cargo/bin


Test Ediyoruz

cargo concordium --help  
Aşşağıdaki çıktıyı alıyorsunuz başarılı şekilde yüklediniz demektir.
Çıktı:

cargo-concordium 0.2.0
Smart contract development tool for building, testing and deploying.

USAGE:
    cargo concordium <SUBCOMMAND>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

SUBCOMMANDS:
    build    Build a deployment ready smart-contract module.
    help     Prints this message or the help of the given subcommand(s)
    run      Locally simulate invocation method of a smart contract and inspect the state.
    test     Build and run tests using a Wasm interpreter.
