# Repeating-Key Vigenere Technique

### Test Cases
```csharp
string mainPlain = "wearediscoveredsaveyourself";
string mainCipher = "zicvtwqngrzgvtwavzhcqyglmgj".ToUpper();
string mainKey = "deceptive";

string newPlain = "MICHIGANTECHNOLOGICALUNIVERSITY".ToLower();
string newCipher = "TWWNPZOAASWNUHZBNWWGSNBVCSLYPMM".ToUpper();
string newKey = "HOUGHTON".ToLower();
```
### 1. Encrypt Algorithm
```csharp
public string Encrypt(string plainText, string key)
{
    char[] vigenereArray = { 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z' };

    // Repeating key
    string newKey = key;
    if (key.Length < plainText.Length)
    {
        newKey = string.Concat(Enumerable.Repeat(key, (plainText.Length - key.Length)));
    }

    // Getting the plainText
    char p, k;
    int kIndex, pIndex, cIndex;
    string cipherText = "";
    for (int i = 0; i < plainText.Length; i++)
    {
        p = plainText.ToLower()[i];
        k = newKey.ToLower()[i];

        pIndex = Array.IndexOf(vigenereArray, p);
        kIndex = Array.IndexOf(vigenereArray, k);

        cIndex = (pIndex + kIndex) % 26;
        cipherText += vigenereArray[cIndex];
    }
    return cipherText;
}
```

### 2. Decryption Algorithm

```csharp
public string Decrypt(string cipherText, string key)
{
    char[] vigenereArray = { 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z' };

    // Repeating key
    string newKey = key;
    if (key.Length < cipherText.Length)
    {
        newKey = string.Concat(Enumerable.Repeat(key, (cipherText.Length - key.Length)));
    }

    // Getting the plainText
    char c, k;
    int cIndex, kIndex, pIndex;
    string plainText = "";
    for (int i = 0; i < cipherText.Length; i++)
    {
        c = cipherText.ToLower()[i];
        k = newKey.ToLower()[i];

        cIndex = Array.IndexOf(vigenereArray, c);
        kIndex = Array.IndexOf(vigenereArray, k);

        pIndex = (cIndex - kIndex + 26) % 26;
        plainText += vigenereArray[pIndex];

    }
    return plainText;
}
```

### 3. Analysis

```csharp
public string Analyse(string plainText, string cipherText)
{
    char[] vigenereArray = { 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z' };

    // Getting the key
    char c, p;
    int cIndex, pIndex, kIndex;
    string key = "";
    for (int i = 0; i < cipherText.Length; i++)
    {
        p = plainText.ToLower()[i];
        c = cipherText.ToLower()[i];

        pIndex = Array.IndexOf(vigenereArray, p);
        cIndex = Array.IndexOf(vigenereArray, c);

        kIndex = (cIndex - pIndex + 26) % 26;
        key += vigenereArray[kIndex];

    }
    string cleanKey = RemoveRepeatedLetters(key);
    return cleanKey;

    string RemoveRepeatedLetters(string word)
    {
        string newWord = "";
        for (int i = 0; i < word.Length - 1; i++)
        {
            if (word[i + 1] == word[0] && word[i + 2] == word[1])
            {
                newWord += word[i];
                break;
            }
            newWord += word[i];
        }
        return newWord;
    }
}
```