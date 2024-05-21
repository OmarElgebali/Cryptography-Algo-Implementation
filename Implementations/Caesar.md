# Caesar Technique
### Test Cases
```csharp
string mainPlain = "meetmeaftertheparty";
string mainCipher = "phhwphdiwhuwkhsduwb".ToUpper();
int mainKey = 3;

string mainPlain1 = "defendtheeastwallofthecastle";
string mainCipher1 = "defendtheeastwallofthecastle".ToUpper();
int mainKey1 = 0;

string mainPlain2 = "defendtheeastwallofthecastle";
string mainCipher2 = "bcdclbrfccyqruyjjmdrfcayqrjc".ToUpper();
int mainKey2 = 24;

string mainPlain3 = "thequickbrownfoxjumpoverthelazydog";
string mainCipher3 = "WKHTXLFNEURZQIRAMXPSVRYHUWKHODCBGRJ".ToUpper();
int mainKey3 = 3;
```
### 1. Encrypt Algorithm
```csharp
public string Encrypt(string plainText, int key)
{
    string cipherText = "";
    int asciiPlainText;
    char asciiCipherChar;
    for (int i = 0; i < plainText.Length; i++)
    {
        asciiPlainText = Convert.ToInt32(plainText[i])-97;
        asciiCipherChar = Convert.ToChar(((asciiPlainText + key) % 26)+65);
        cipherText+=(Convert.ToChar(asciiCipherChar));

    }
    return cipherText;
}
```
### 2. Decryption Algorithm
```csharp
public string Decrypt(string cipherText, int key)
{
    key = key % 26;
    string plainText = "";
    char asciiPlainText;
    int asciiCipherChar;
    int temp;
    for (int i = 0; i < cipherText.Length; i++)
    {
        asciiCipherChar = Convert.ToInt32(cipherText[i]) - 65;
        temp = (asciiCipherChar - key) % 26;
        temp = ((int)(temp + 26)) % 26;
        asciiPlainText = Convert.ToChar((int)(temp + 97));
        plainText += (Convert.ToChar(asciiPlainText));

    }
    return plainText;

}
```
### 3. Analysis
```csharp
public int Analyse(string plainText, string cipherText)
{   
    plainText=plainText.ToLower();
    cipherText=cipherText.ToLower();    
    int asciiPlainText = Convert.ToInt32(plainText[0]) ;
    int asciiCipherText = Convert.ToInt32(cipherText[0]);
    int key = Math.Abs(asciiPlainText - asciiCipherText);

    if (asciiPlainText>asciiCipherText)
    {
        key = 26-(asciiPlainText-asciiCipherText);
    }
    return key;
}
```