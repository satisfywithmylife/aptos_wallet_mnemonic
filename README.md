# aptos_wallet_mnemonic
## python project about generate mnemonic to aptos hd-wallet 
a tool easy to use bip39,bip44,curve ed25519 to generate a 12 words mnemonic seed such as 
### "extend oven track off route gather next hawk riot delay list merit"
and init a aptos wallet, same result like martian wallet or other aptos chrome wallet extensions
###  

official get faucet and send apt token to another address 
```
import sys
from aiohttp import Payload
from mnemonic import Mnemonic

from utils import PublicKeyUtils
from aptos_sdk.account import Account
from aptos_sdk.client import FaucetClient, RestClient

FAUCET_URL = 'https://faucet.devnet.aptoslabs.com' #'https://faucet.testnet.aptoslabs.com'
NODE_URL = 'https://fullnode.devnet.aptoslabs.com/v1' #'https://fullnode.testnet.aptoslabs.com/v1'


mnemonic_alice = Mnemonic('english').generate()
mnemonic_bob = Mnemonic('english').generate()

print(f'mnemonic_alice is: {mnemonic_alice}')
print(f'mnemonic_bob is: {mnemonic_bob}')

pt_alice = PublicKeyUtils(mnemonic_alice)
pt_bob = PublicKeyUtils(mnemonic_bob)


alice = Account.load_key(pt_alice.private_key.hex())
bob = Account.load_key(pt_bob.private_key.hex())



print("\n=== Addresses ===")
print(f"Alice addresss: {alice.address()}")
print(f"Alice public_key: {alice.public_key()}")
print(f"Alice private_key: 0x{pt_alice.private_key.hex()}")

print(f"Bob addresss: {bob.address()}")
print(f"Bob public_key: {bob.public_key()}")
print(f"Bob private_key: 0x{bob.private_key.hex()}")

rest_client = RestClient(NODE_URL)
faucet_client = FaucetClient(FAUCET_URL, rest_client)  # <:!:section_1


#:!:>section_3
faucet_client.fund_account(alice.address(), 1_000_000_0)
faucet_client.fund_account(bob.address(), 0)  # <:!:section_3

print("\n=== Initial Balances ===")
#:!:>section_4
print(f"Alice: {rest_client.account_balance(alice.address())}")
print(f"Bob: {rest_client.account_balance(bob.address())}")  # <:!:section_4

# Have Alice give Bob 1_000 coins
#:!:>section_5
txn_hash = rest_client.transfer(alice, bob.address(), 1_000_000_0)  # <:!:section_5
#:!:>section_6
rest_client.wait_for_transaction(txn_hash)  # <:!:section_6

print("\n=== Intermediate Balances ===")
print(f"Alice: {rest_client.account_balance(alice.address())}")
print(f"Bob: {rest_client.account_balance(bob.address())}")

# Have Alice give Bob another 1_000 coins using BCS
txn_hash = rest_client.bcs_transfer(alice, bob.address(), 1_000_000_0)
rest_client.wait_for_transaction(txn_hash)

print("\n=== Final Balances ===")
print(f"Alice: {rest_client.account_balance(alice.address())}")
print(f"Bob: {rest_client.account_balance(bob.address())}")

rest_client.close()
```

generate hd-wallet

```
words = Mnemonic('english').generate()
print(words)

for address_index in range(10):
    # Derivation from BIP44 derivation path
    path = f"m/44'/637'/{address_index}'/0'/0'"
    pt = PublicKeyUtils(words, path)
    apt_account = Account.load_key(pt.private_key.hex())
    print(f"({address_index}) {path} {apt_account.address()} 0x{pt.private_key.hex()}")
```
result

```
extend oven track off route gather next hawk riot delay list merit

(0) m/44'/637'/0'/0'/0' 0x3e81c1268a9016655e77a6b4191154e37b8f5c5c37aaaa7ffc9dc63dea935ac8 0x0950979cdade001a91fb82bb60a35c3839c31ce92fbb252888d13422f2ea0d1f
(1) m/44'/637'/1'/0'/0' 0x64d7a147497ec6b87ed0f81eac214f6e944cf2e9ea4589a555d7b31e7063d71b 0x6bfdbbe7f7170c641ecbbe60d3d4a6850a3f5c1b2831a87f2ed5eb1e20417967
(2) m/44'/637'/2'/0'/0' 0x3264ce9de150dc22199aadfeabbac2b4e4187b78f128f460270e92541cf7a5ff 0xb686b47242d10e593e2647eb7dd1c85b0c138552a8df8a5827c1b668025369f0
(3) m/44'/637'/3'/0'/0' 0x6d4a8160735eabe9f9c37a8f75fd013fd2c81f4adc17a1fe5cc2bd9b34ff140e 0xe4e7a21fa88026466671ee19dc189bb81c8aa87275cd5a8e7b69b82eedde05a1
(4) m/44'/637'/4'/0'/0' 0xb9a540c0cd5bec9d158041880b0deb2799c524cb3ee099b7830036946f996bfa 0x761885cdd73a22ad847d69e2e0aae3fc56120d62276e62b22722bb44ae72746b
(5) m/44'/637'/5'/0'/0' 0x81441cbd3dc8181f3a671b20ee0240a8ec50b8eb407acd8c52c76a06623df19d 0x8ec5add95c473cf777ec6ad17febe7a58f7a78e001e1f3de3c38cda7e67db5dc
(6) m/44'/637'/6'/0'/0' 0x9eb18b125c89361798d18b036e1fa29c0834181b2d99ffac7b8ddc78dc7e4e6b 0x9dde768f98a96c4cf94720a6615661b06a91c30fd9748627e5377c751605a3f9
(7) m/44'/637'/7'/0'/0' 0x33b235ee43550c77aef7c77ca3b607bc4c147070fb23b5e9f2477de1404b452b 0xbd2d859f8b4c3d5055a18eccbbc9f88a0e243408f60565a6dba5a3fbf520868f
(8) m/44'/637'/8'/0'/0' 0x8dc9282c6f57765addbfff6e1e7437c7df75c4b6f851cf8d1d73d0d703167e67 0x6d372043d7ebe03affd7f0a6658109a024206f1012d6ff181a174bd1d51f0c55
(9) m/44'/637'/9'/0'/0' 0xa3639133622b2ff580ad190570a095f0e03f4e62047f6ae9ce90dec92af7f517 0x5e95eab75e080985eb4ab47f81813001fe9c6aa4099981f981cf02da2383c70a
```

if we add the mnemonic seeds to martian wallet , you can see the same result

![image](https://user-images.githubusercontent.com/30144807/193611533-e310fa26-b89f-4279-a3b8-45a811fe6b07.png)
![1664809788942](https://user-images.githubusercontent.com/30144807/193612203-c6699ab8-b718-47c3-8c04-f478513aa896.png)




