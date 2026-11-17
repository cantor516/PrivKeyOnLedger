# PrivKeyOnLedger
Guides for generating a key pair using 'OpenPGP' on the 'Ledger nano s' device.  


이 문서는,가상화폐 Hardware 지갑인,'Ledger nano s'에 'OpenPGP'앱을 설치한후 PC와 연결하여 Gnupg에 기반한 Key pair를 생성하기 까지의 과정을 소개하고,또한 'Ledger nano s' 를 암호화 작업에서 이용하는 방법에 대해서 설명하는 것이 목적이다. 
## 1 왜 hardware device에서 key pair를 생성하는가?
통상적으로는 PC에서 key pair를 생성해서 사용하는데,key pair생성시 passphrase라는 암호를 요구한다. passphrase는 private key를 PC에 안전하게 보관하기 위해 사용하는 암호화key이며, 동시에, 사용자가 그key의 owner인지 여부를 확인할때도 사용된다. 즉,passphrase를 망각하면,설사 private key가 있더라도 무용지물이 된다. 즉,key pair와 passphrase는 일심동체다. 한편,PC의 고장/손망실 등 비상사태에 대비하여,private key의 backup file을 만들어 두어야 하는데, 여기에는 다음 2가지 option이 있다.
- 암호화 하지 않은 backup file을, 가령 USB 저장장치에, 저장후 그것을 절대로 안전장소에 note와 함께 장기보관한다.\
( 중요! : Note에는 passphrase를 필히 기록해둘것.) 
- backup 파일 생성시,암호화를 적용한다. 이경우 backup 파일 자체보다 암호화에 적용했던 암호가 더 중요하다. 강력한 암호화 key를 사용했다면, backup 파일 자체는 여기 저기 다양한 장소에 안심하고 저장해 두어도 무방하다. 이경우 철저히 보안을 유지해야 할 정보는\
(1) backup파일 이름                 