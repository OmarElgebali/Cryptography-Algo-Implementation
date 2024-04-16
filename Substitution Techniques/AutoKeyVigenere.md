# Auto-Key Vigenere Technique

### Test Cases
```csharp
string mainPlain = "wearediscoveredsaveyourself";
string mainCipher = "zicvtwqngkzeiigasxstslvvwla".ToUpper();
string mainKey = "deceptive";

string newPlain = "MICHIGANTECHNOLOGICALUNIVERSITY".ToLower();
string newCipher = "TWWNPZOAFMEOVULBZMEHYIYWBMTSTNL".ToUpper();
string newKey = "HOUGHTON".ToLower();
```
### 1. Encrypt Algorithm
```csharp
public string Encrypt(string plainText, string key)
{
    string cipher = "";
    string alphabites = "abcdefghijklmnopqrstuvwxyz";
    int diff = plainText.Length - key.Length;
    string key_stream = key;
    Dictionary<char, int> vigenere_tableau = new Dictionary<char, int>();
    for (int i = 0; i < alphabites.Length; i++)
    {
        vigenere_tableau.Add(alphabites[i], i);
    }
    for (int i = 0; i < key_stream.Length; i++)
    {
        if (i < plainText.Length)
        {
            int pIndex = vigenere_tableau[plainText[i]];
            int kIndex = vigenere_tableau[key_stream[i]];
            int far = (pIndex + kIndex) % 26;
            cipher += alphabites[far];
            key_stream += plainText[i];
        }
        else
            break;
    }

    return cipher;
}
```

### 2. Decryption Algorithm

```csharp
public string Decrypt(string cipherText, string key)
{
    string plainText = "";
    cipherText = cipherText.ToLower();
    string alphabites = "abcdefghijklmnopqrstuvwxyz";
    string key_stream = key;
    Dictionary<char, int> vigenere_tableau = new Dictionary<char, int>();
    for (int i = 0; i < alphabites.Length; i++)
    {
        vigenere_tableau.Add(alphabites[i], i);
    }
    for (int i = 0; i < key_stream.Length; i++)
    {
        if (i < cipherText.Length)
        {
            int cIndex = vigenere_tableau[cipherText[i]];
            int kIndex = vigenere_tableau[key_stream[i]];
            int far = cIndex - kIndex;
            if (far < 0)
                far += 26;
            plainText += alphabites[far];
            key_stream += alphabites[far];
        }
        else
            break;
    }
    Console.WriteLine(plainText);
    Console.WriteLine(cipherText);
    Console.WriteLine(key_stream);
    return plainText;
}
```

### 3. Analysis

```csharp
public string Analyse(string plainText, string cipherText)
{
    string key_stream = "";
    cipherText = cipherText.ToLower();
    string alphabites = "abcdefghijklmnopqrstuvwxyz";
    Dictionary<char, int> vigenere_tableau = new Dictionary<char, int>();
    for (int i = 0; i < alphabites.Length; i++)
    {
        vigenere_tableau.Add(alphabites[i], i);
    }
    for (int i = 0; i < plainText.Length; i++)
    {
        int pIndex = vigenere_tableau[plainText[i]];
        int cIndex = vigenere_tableau[cipherText[i]];
        int far = cIndex - pIndex;
        if (far < 0)
            far += 26;
        key_stream += alphabites[far];
    }
    Queue<int> substring = new Queue<int>();
    int count = 0;
    for (int i = 0; i < plainText.Length; i++)
    {
        for (int j = 0;  j < key_stream.Length;j++)
        {
            if (key_stream[j] == plainText[i])
            {
                count++;
                substring.Enqueue(j);
                i++;
            } else if (count > 0 && key_stream[j] != plainText[i]) 
            {
                i = 0;
                count = 0;
                substring.Clear();
            }
        }
        break;
    }
    key_stream = key_stream.Remove(substring.Dequeue());
    return key_stream;
    
}
```