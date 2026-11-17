# Hardware device를 쓰는 몇가지 이유
현재 대부분의 경우 PC(Linux/Windows)에서 key pair를 생성해서 사용한다. 그런데 key pair생성시 passphrase라는 암호를 요구하는 과정이 반드시 있다. 이 passphrase를 망각하면,설사 private key가 있더라도 사실상 없는 것이나 마찬가지 상태가 된다. private key와 passphrase는 일심동체라고 봐야한다. 반드시 명심해야 할 사항이다.

한편,PC의 고장/손망실 등등 비상사태에 대비하여, private key에 대한 backup file을 만들어 두어야 한다. private key에 대한 비밀 관리 라고하는 귀찮은 문제가 따르게 되는 것이다.  

Section1 에서는 기존의 방식의 약점/복잡성에 대해서 설명하고,\
Section2 에서는 hardware device에 private key를 두고 사용하는 방식의 장점에 대해서 설명하려고 한다.   

## 1 기존방식의 귀찮은 점들
여기서 '기존방식'이란 key pair를 PC에서 생성하고,또한,그 private key가 암호화 된(passphrase에 의하여)형태로,PC내에 저장되어 있는 경우를 말한다. 현대의 PC는 Internet에 연결하여 사용되므로 외부의 hacker나 spy(?)등등에 대비하여 private key를 passphrase로 암호화 해서 저장해둘 수 밖에 없는 것이다. passphrase를 기억 해야 하는 것이 첫번째로 감수해야 하는 귀찮은 부담이다.\

그리고,우리는 PC자체의 고장/손망실 에도 대비하여야 한다. 즉,private key에 대한 backup 파일을 만들어 따로 안전한 곳에 보관해 두어야 하는 것이다.\
여기에는 다음 2가지 option이 있다.

1) 암호화 하지 않은 backup file을, 가령 USB 저장장치에, 저장후 그것을 절대로 안전장소에 '메모'와 함께 장기보관한다.\
( 중요! : '메모'에는 최소한 passphrase 정보가 반드시 들어가야 할것이다.) 

2) backup 파일을 암호화 하여,그것을 보관한다. 강력한 암호화 key를 사용한다면, 암호화된 backup파일 자체는 여기저기 분산 저장해도 좋고 보안유지에 특별히 신경쓸 일은 없다. 그러나,다음 3가지 정보는(특히 처음 2개의 정보)철저한 보안을 필요로 하므로,note에 적어 비밀한 장소에 밀봉함이 좋다.\
\
(1)backup 파일 암호화 시 사용했던 암호화key\
(2)private key의 passphrase\
(3)암호화된 backup 파일명 (오랜시간이 지나면 망각하기 쉽다!).


내 경우는, 위의 Option 2 를 쓰지만, passphrase와 backup암호화key 정보 등등을 symmetric encryption을 써서 암호파일로 만들어 두는 방식을 취한다.
이를 구체적으로 Step by step 으로 설명한다면. . .

1단계로 다음처럼 private key를 위한 backup파일 private-key-backup.asc를 만든다.\
(이 파일은 ASCII file 형식이며,전혀 암호화 되어있지 않다) 
```
gpg --armor --export-secret-keys --output private-key-backup.asc YOUR_KEY_ID
```
2단계로, 다음처럼 위에서 얻은 출력 파일을 symmetric encryption 한다. 당연히 암호화 key를 물어 올것이다. 암호화된 최종 backup 파일은  private-key-backup.asc.gpg 이다.
```
gpg -c private-key-backup.asc > private-key-backup.asc.gpg
```

이 파일 
private-key-backup.asc.gpg 은 암호화가 되어 있기 때문에 특별히 보안에 신경쓸 일이 없다.  그보다는 (1)passphrase,(2)backup파일 암호화key (3) 암호화backup 파일명\
의 정보를(특히 첫 두항목) 철저히 안전하게 보관하는 것이 중요하다! 

그래서 3단계는\
위의 3가지 중요정보를 나만이 아는 '개인암호'를 key로 하여  symmetric encryption 하는 것이다.  그런데,이 3가지 정보의 기록을 위해 새로운 파일을 만들기도 또한 번거로우므로 내경우 보통 다음 같이 'Here Document' 기법을 종종 쓴다. 없애야 할 file이 생기지도 않아서 편리하다!

```
gpg -c > mysecret2026.sym <<END
>passphrase = xxxxx
>backup file enc key = yyyyy
>backup file name = private-key-backup.asc.gpg
>END
```
그후 folder를 하나 만들어, 그속에\
private-key-backup.asc.gpg\
mysecret2026.sym\
Readme.txt

이 3개의 file을 두되, Readme.txt 파일에는 mysecret2026.sym을 encrypt 할때 썼었던 그 '개인암호'에 대한 hint(남이 보면 도저히 알수 없는...)는 최소한 포함해야 할것이고,기타 이 key pair를 생성했었는지 등등 추가적 참고사항을 써두는 것이다.  이 폴더는 PC에 두어서는 않되고, 그 존재 의미상,PC가 아닌 다른곳,예를들어 USB drive, Google Drive 같은 곳에 저장해 두어야 할것이다.   

적어도 이정도의 노력을 해야, 비로써 그 key pair를 써서 안심하고 중요한 정보를 아주 장기적으로 안전하게 보관할수 있다.

이렇게 잘 대비하고 있더라도,아주 중대한 비밀 또는 막대한 금액의 자산이 이 private key에 의하여 보호받고 있다면, PC에 저장되어 있는 private key,비록 passphrase로 protect되어 있다고는 하지만 "혹시라도?"하는 불안한 마음이 완전히 가시지는 않을 것이다!  

## 2 Hardware device 방식
이 방식은 '하드웨어 보안키'(혹은 '보안 token')라고 불리우는 조그만 device를 이용하는 방식인데. 
Device 자체에서 key pair를 생성할 수 있다. 특기할 점은 생성된 private key정보는 어떠한 경우든 device밖으로는 절대 나갈수 없도록 설계되어 있다. PC와는 USB port를 통해서 서로 소통하며 작동한다.

대표적인 제품이 Yubico 사의 Yubikey이다.  이런 방식의 장점은 private key가 PC와 분리 된 device내에 있으므로 passphrase 같은 것이 필요없다!  (device에서 key pair생성시 passphrase를 물어오지 않는다!)
그러므로,device를 잘 간수하고 있기만 하면 된다. 단, device 분실/고장 시 어떻게 private key를 복원할 것인가에 대한 대책은 반드시 필요하고 이것은 device의 제작사에 따라 다를 것이다. 

'Ledger nano s'란 원래는 '가상화폐 hardware 지갑'이다. 그런데, 이 device에 'OpenPGP' 라는 앱을 설치하고 그 앱을 선택하여 들어가면,위에서 말한 '보안 key'처럼 작동하게 되는 것이다~!   
이 repository의 이야기는 바로 이 'Ledger nano s' device에 'OpenPGP' 앱을 설치한 후 어떻게 USB를 통해 PC와 연동하고,어떻게 key pair를 generation하는지,어떻게 암호화 작업에 활용하는지에 대한 
것이다. 

## 3 Ledger nano s의  24 words recovery phrase 

Device를 구매후 최초 setup시 24개의 영어단어를 보이면서 반드시 받아 적도록 요구하는데, 이것을 "24 words recovery phrase"라고 말한다. 모든 가상화폐 hardware지갑들이 공통적으로 거치는 과정이다. 이것은 바로,혹시라도 이 device가 고장나거나 손망실 되는 경우에 대비하여,(새로운 device를 구입한후),전의 상태로 되돌리기 위한 복구용 정보이다. 이것이 타인에게 노출되면 가상화폐를 전부 잃게 될 것이므로,철저한 비밀이 유지되어야 하고 적어도 2개 이상의 저장장소나 저장방법이 강구되어야 한다.      

'Ledger nano s'에 'OpenPGP' 앱을 설치한후, private key를 device내부적으로 생성해서 사용하는 경우,private key는 어떤 경우든 결코 device 밖으로 노출되는 일은 없으므로 private key정보의 노출 가능성은 걱정할 필요가 없다!\
혹시 비상사태가 생길수 있다면, 다음 2가지 경우 일 것이다.

1) Ledger nano s 의 분실.
2) Ledger nano s의 고장/파손

위 1의 경우 devide를 습득한 자가 'Ledger nano s'를 이용하여 가상화폐 자산에 접근할 가능성을 우려할 수 있는데, 이런 사태에 대한 대책이 바로 PIN code 이다. Device에 전원이 인가 되면,늘 PIN code 입력을 요구하는데,4회 이상 틀린 값을 입력하면 Ledger nano s는 '공장초기화'상태로 되어 버린다! 즉,owner의 비밀을 보호하기 위한 안전조치인 것이다!

그러므로 분실한 경우에도 걱정할 필요는 없다. 2의 고장/파손 의 경우와 마찬가지로, device를 새로 구입한 후, 초기 setup에서 "복구"를 선택, 위에서 말한 24 words를 입력함으로써, 적어도 가상화폐에 관한한, 복구는 완료 되는 것이다.

그러나, 우리가 사용하던 바로 그 Private key들을 복원하기 위해서는 우선 'OpenPGP' 앱을 device에 다시 설치해야 하고, README.md 에서 설명하는 수순에 따라,소위 "재현성 있는 key pair generation"을 수행해야 한다.( Deterministic key pair Generation! )        






