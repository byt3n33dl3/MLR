# WannaCry | Ransomware Worm 

* **Virus Name**: WannaCrypt, WannaCry, WanaCrypt0r, WCrypt, WCRY
* **Vector**: All Windows versions before Windows 10 are vulnerable if not patched for MS-17-010. It uses EternalBlue MS17-010 to propagate.
* **Ransom**: between $300 to $600. There is code to 'rm' (delete) files in the virus. Seems to reset if the virus crashes.
* **Backdooring**: The worm loops through every RDP session on a system to run the ransomware as that user. It also installs the DOUBLEPULSAR backdoor. It corrupts shadow volumes to make recovery harder. (source: malwarebytes)
* **Kill switch**: If the website `www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com` is up the virus exits instead of infecting the host. (source: malwarebytes). This domain has been sinkholed, stopping the spread of the worm. Will not work if proxied ([source](https://blog.didierstevens.com/2017/05/13/quickpost-wcry-killswitch-check-is-not-proxy-aware/)).

## Liability Disclaimer:
To the maximum extent permitted by applicable law, I shall not be held liable for any indirect, incidental, special, consequential or punitive damages, or any loss of profits or revenue, whether incurred directly or indirectly, or any loss of data, use, goodwill, or other intangible losses, resulting from (i) your access to this resource and/or inability to access this resource; (ii) any conduct or content of any third party referenced by this resource, including without limitation, any defamatory, offensive or illegal conduct or other users or third parties; (iii) any content obtained from this resource


# Vulnerable/Not Vulnerable

To be infected requires the SMB port (445) to be open, or the machine already infected with DOUBLEPULSAR (and killswitch not registered or somehow blocked, or the network accessing it through a proxy).

The MS17-010 patch fixes the vulnerability.

* Windows XP: Doesn't spread. If run manually, can encrypt files.
* Windows 7,8,2008: can spread unpatched, can encrypt files.
* Windows 10: Doesn't spread. Even though Windows 10 [does have the faulty SMB driver](http://www.infoworld.com/article/3196825/microsoft-windows/how-to-make-sure-your-windows-pc-wont-get-hit-by-ransomware-like-wannacrypt.html).
* Linux: Doesn't spread. If run manually with wine, can encrypt files.

# Infections
![wannaCry](https://github.com/Sulaimannabdul/Ransomware-Worm/assets/151133481/6d17b0b8-2daf-4010-b1f4-b765ee355b9a)

# Cryptography details

* Each infection generates a new RSA-2048 keypair.
* The public key is exported as blob and saved to 00000000.pky
* The private key is encrypted with the ransomware public key and saved as 00000000.eky
* Each file is encrypted using AES-128-CBC, with a unique AES key per file.
* Each AES key is generated CryptGenRandom.
* The AES key is encrypted using the infection specific RSA keypair.

The RSA public key used to encrypt the infection specific RSA private key is embedded inside the DLL and owned by the ransomware authors.

* https://haxx.in/key1.bin (the ransomware pubkey, used to encrypt the users private key)
* https://haxx.in/key2.bin (the dll decryption privkey)
the CryptImportKey() rsa key blob dumped from the DLL by blasty.

https://pastebin.com/aaW2Rfb6 even more in depth RE information by cyg_x1!!


# Bitcoin ransom addresses

3 addresses hard coded into the malware.

* https://blockchain.info/address/13AM4VW2dhxYgXeQepoHkHSQuy6NgaEb94
* https://blockchain.info/address/12t9YDPgwueZ9NyMgw519p7AA8isjr6SMw
* https://blockchain.info/address/115p7UMMngoj1pMvkpHijcRdfJNXj6LrLn


# Languages

All language ransom messages available here

m_bulgarian, m_chinese (simplified), m_chinese (traditional), m_croatian, m_czech, m_danish, m_dutch, m_english, m_filipino, m_finnish, m_french, m_german, m_greek, m_indonesian, m_italian, m_japanese, m_korean, m_latvian, m_norwegian, m_polish, m_portuguese, m_romanian, m_russian, m_slovak, m_spanish, m_swedish, m_turkish, m_vietnamese

# File types

There are a number of files and folders wannacrypt will avoid. Some because it's entirely pointless and others because it might destabilize the system. During scans, it will search the path for the following strings and skip over if present:

*   "Content.IE5"
*   "Temporary Internet Files"
*   " This folder protects against ransomware. Modifying it will reduce protection"
*   "\Local Settings\Temp"
*   "\AppData\Local\Temp"
*   "\Program Files (x86)"
*   "\Program Files"
*   "\WINDOWS"
*   "\ProgramData"
*   "\Intel"
*   "$\"

The filetypes it looks for to encrypt are:

.doc, .docx, .xls, .xlsx, .ppt, .pptx, .pst, .ost, .msg, .eml, .vsd, .vsdx, .txt, .csv, .rtf, .123, .wks, .wk1, .pdf, .dwg, .onetoc2, .snt, .jpeg, .jpg, .docb, .docm, .dot, .dotm, .dotx, .xlsm, .xlsb, .xlw, .xlt, .xlm, .xlc, .xltx, .xltm, .pptm, .pot, .pps, .ppsm, .ppsx, .ppam, .potx, .potm, .edb, .hwp, .602, .sxi, .sti, .sldx, .sldm, .sldm, .vdi, .vmdk, .vmx, .gpg, .aes, .ARC, .PAQ, .bz2, .tbk, .bak, .tar, .tgz, .gz, .7z, .rar, .zip, .backup, .iso, .vcd, .bmp, .png, .gif, .raw, .cgm, .tif, .tiff, .nef, .psd, .ai, .svg, .djvu, .m4u, .m3u, .mid, .wma, .flv, .3g2, .mkv, .3gp, .mp4, .mov, .avi, .asf, .mpeg, .vob, .mpg, .wmv, .fla, .swf, .wav, .mp3, .sh, .class, .jar, .java, .rb, .asp, .php, .jsp, .brd, .sch, .dch, .dip, .pl, .vb, .vbs, .ps1, .bat, .cmd, .js, .asm, .h, .pas, .cpp, .c, .cs, .suo, .sln, .ldf, .mdf, .ibd, .myi, .myd, .frm, .odb, .dbf, .db, .mdb, .accdb, .sql, .sqlitedb, .sqlite3, .asc, .lay6, .lay, .mml, .sxm, .otg, .odg, .uop, .std, .sxd, .otp, .odp, .wb2, .slk, .dif, .stc, .sxc, .ots, .ods, .3dm, .max, .3ds, .uot, .stw, .sxw, .ott, .odt, .pem, .p12, .csr, .crt, .key, .pfx, .der

credit herulume, thanks for extracting this list from the binary.

more details came from https://pastebin.com/xZKU7Ph1 thanks to cyg_x11

# Some other interesting strings 

* BAYEGANSRV\administrator
* Smile465666SA
* wanna18@hotmail.com

credit: nulldot https://pastebin.com/0LrH05y2

# Encrypted file format

```
typedef struct _wc_file_t {
    char     sig[WC_SIG_LEN]     // 64 bit signature WANACRY!
    uint32_t keylen;             // length of encrypted key
    uint8_t  key[WC_ENCKEY_LEN]; // AES key encrypted with RSA
    uint32_t unknown;            // usually 3 or 4, unknown
    uint64_t datalen;            // length of file before encryption, obtained from GetFileSizeEx
    uint8_t *data;               // Ciphertext Encrypted data using AES-128 in CBC mode
} wc_file_t;
```
