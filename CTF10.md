# CTF Semana #10 (Classical Encryption)

## Objective

The objective was to decipher a given encrypted text and extract the hidden flag enclosed within `{}`.

---

## Cipher Characteristics

The given cipher was a substitution cipher where each symbol in the ciphertext corresponded to a unique character in plaintext. It was noted that the flag was enclosed within `{}` and these characters weren't ciphered. It was also noted that the text would decipher to a portuguese text.

The ciphertext was:
```
&>);,)<*/;)*[;*@,)(,*.[;.;[?/@[*@)<):,>;):)[~[-;>.[~):*;)_/:)~*@[>@*%)@&>*)-;>.))/(~))@<)#;/:)@)_);[()_);[()-;*@*:*;/(~)<[/*_~*_)/@~*:/(:[_/%|[*@~*:[(,[@:[(,;)&>),;[_/%|[*@*_.);)*@,*)([)):,/?/~)~*~)@*_.;*@)@~*?*.;[.[;[:/[();>_))>_*(,[~*:*;:)~*~*+.[;:*(,[([?[%>_*~*?*(~)@([*(,)(,[)~/_/(>/:)[~)):,/?/~)~*~):[(@,;>:)[:/?/%~*|)#/,):)[([@>%,/_[@_*@*@.[~*;)?/;))<*:,);*@,)@.;*?/@[*@*@,)(~[[@;*@.[(@)?*/@~)@:*;)_/:)@()*!.*:,),/?)~[&>*[<>,>;[.;[!/_[.[~*?/;);*@*;?);;-)@~*:/@[*@:[(,;)~/,[;/)@~[#)(:[:*(,;)%)%*{|%>@_;|_*-~(?;~@}
```

---

## Decryption Process

We created a python script to change each character of the ciphertext to a upper case letter, in order to be easier to decipher.

Substitution Dictionary:
```python
substitution = {
    "&": "A",
    ">": "E",
    ")": "O",
    ";": "S",
    ",": "L",
    "<": "I",
    "*": "N",
    "/": "D",
    "[": "C",
    "@": "T",
    ":": "M",
    ".": "U",
    "?": "B",
    "|": "P",
    "~": "V",
    "_": "G",
    "#": "H",
    "!": "F",
    "(": "Z",
    "+": "J",
    "-": "X",
    "^": "K",
    "=": "Y",
    "]": "W",
    "%": "Q",
}
```

After applying this substitution, the ciphertext was converted to:
```
AEOSLOINDSONCSNTLOZLNUCSUSCBDTCNTOIOMLESOMOCVCXSEUCVOMNSOGDMOVNTCETNQOTAENOXSEUOODZVOOTIOHSDMOTOGOSCZOGOSCZOXSNTNMNSDZVOICDNGVNGODTVNMDZMCGDQPCNTVNMCZLCTMCZLSOAEOLSCGDQPCNTNGUOSONTLNOZCOOMLDBDVOVNVOTNGUSNTOTVNBNUSCUCSCMDCZOSEGOOEGNZLCVNMNSMOVNVNJUCSMNZLCZCBCQEGNVNBNZVOTZCNZLOZLCOVDGDZEDMOCVOOMLDBDVOVNVOMCZTLSEMOCMDBDQVNPOHDLOMOCZCTEQLDGCTGNTNTUCVNSOBDSOOINMLOSNTLOTUSNBDTCNTNTLOZVCCTSNTUCZTOBNDTVOTMNSOGDMOTZONFUNMLOLDBOVCAENCIELESCUSCFDGCUCVNBDSOSNTNSBOSSXOTVNMDTCNTMCZLSOVDLCSDOTVCHOZMCMNZLSOQOQN{PQETGSPGNXVZBSVT}
```

## Frequency Analysis

We used the website https://www.dcode.fr/frequency-analysis in order to perform a frenquency analysis in the text (after doing the substitution for improved readibility).
![websitePhoto](/docs/images/ctf10.jpg)
<br>

We looked at the most frequent letters in the portuguese letters and found out that these are the most common: A, E e O.
We applied those susbtitutions, and figured where some words would end and others start:

```
AEaSLaIeDSaeoSeTLaZLeUoSUSoBDToeTaIaMLESaMaoVoXSEUoVaMeSaGDMaVeToETeQaTAEeaXSEUaaDZVaaTIaHSDMaTaGaSoZaGaSoZaXSeTeMeSDZVaIoDeGVeGaDTVeMDZMoGDQPoeTVeMoZLoTMoZLSaAEaLSoGDQPoeTeGUaSaeTLeaZoaaMLDBDVaVeVaTeGUSeTaTVeBeUSoUoSoMDoZaSEGaaEGeZLoVeMeSMaVeVeJUoSMeZLoZoBoQEGeVeBeZVaTZoeZLaZLoaVDGDZEDMaoVaaMLDBDVaVeVaMoZTLSEMaoMDBDQVePaHDLaMaoZoTEQLDGoTGeTeTUoVeSaBDSaaIeMLaSeTLaTUSeBDToeTeTLaZVooTSeTUoZTaBeDTVaTMeSaGDMaTZaeFUeMLaLDBaVoAEeoIELESoUSoFDGoUoVeBDSaSeTeSBaSSXaTVeMDToeTMoZLSaVDLoSDaTVoHaZMoMeZLSaQaQe
```
For example in "aeo" it should be "a e o", where the left word ends with 'a' and the right one stats with 'o'.

We also analysed 2-grams:
<br>

![websitePhoto2](/docs/images/ctf10-2.jpg)

We changed "NT" (where N is 'e'), by looking at the portuguse most common 2-grams, 'es' starts with e (N in the cipher) and ends with s, so we changed T to 's':

```
AEaSLaIeDSaeoSesLaZLeUoSUSoBDsoesaIaMLESaMaoVoXSEUoVaMeSaGDMaVesoEseQasAEeaXSEUaaDZVaasIaHSDMasaGaSoZaGaSoZaXSeseMeSDZVaIoDeGVeGaDsVeMDZMoGDQPoesVeMoZLosMoZLSaAEaLSoGDQPoeseGUaSaesLeaZoaaMLDBDVaVeVaseGUSesasVeBeUSoUoSoMDoZaSEGaaEGeZLoVeMeSMaVeVeJUoSMeZLoZoBoQEGeVeBeZVasZoeZLaZLoaVDGDZEDMaoVaaMLDBDVaVeVaMoZsLSEMaoMDBDQVePaHDLaMaoZosEQLDGosGesesUoVeSaBDSaaIeMLaSesLasUSeBDsoesesLaZVoosSesUoZsaBeDsVasMeSaGDMasZaeFUeMLaLDBaVoAEeoIELESoUSoFDGoUoVeBDSaSeseSBaSSXasVeMDsoesMoZLSaVDLoSDasVoHaZMoMeZLSaQaQe
```

## Final tool

Then, we used an online tool in order to decipher to the portuguese languangue. The tool was: https://www.guballa.de/substitution-solver

This tool is an online resource specifically designed for solving substitution ciphers. This tool returned the following:

```
QUARTAFEIRAEORESTANTEPORPROVISOESAFACTURACAODOGRUPODACERAMICADESOUSELASQUEAGRUPAAINDAASFABRICASAMARONAMARONAGRESECERINDAFOIEMDEMAISDECINCOMILHOESDECONTOSCONTRAQUATROMILHOESEMPARAESTEANOAACTIVIDADEDASEMPRESASDEVEPROPOROCIONARUMAAUMENTODECERCADEDEZPORCENTONOVOLUMEDEVENDASNOENTANTOADIMINUICAODAACTIVIDADEDACONSTRUCAOCIVILDEHABITACAONOSULTIMOSMESESPODERAVIRAAFECTARESTASPREVISOESESTANDOOSRESPONSAVEISDASCERAMICASNAEXPECTATIVADOQUEOFUTUROPROXIMOPODEVIRARESERVARRGASDECISOESCONTRADITORIASDOBANCOCENTRALALE{HLUSMRHMEGDNVRDS}
```

As we can see, the text makes sense and looks like an article from a portuguese newspaper, as expected.

---

The flag was located within `{}` in the deciphered text and we passed it to lower case:
```
flag{hlusmrhmegdnvrds}
```
---
