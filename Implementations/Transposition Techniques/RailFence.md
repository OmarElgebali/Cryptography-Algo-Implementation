# Rail Fence Technique

### Test Cases

```csharp
int mainKey1 = 2;
string mainPlain1 = "meetmeaftertheparty";
string mainCipher1 = "mematrhpryetefeteat".ToUpper();

int mainKey2 = 3;
string mainPlain2 = "meetmeaftertheparty";
string mainCipher2_1 = "mtaehayemfrereettpt".ToUpper();
string mainCipher2_2 = "mtaehayemfrerxeettptx".ToUpper();
string mainPlain2_1 = "meetmeaftertheparty";
string mainPlain2_2 = "meetmeafterthepartyxx";

int mainkey3 = 2;
string mainPlain3 = "nothingisasitseems";
string mainCipher3 = "NTIGSSTEMOHNIAISES";
```

### 1. Encrypt Algorithm

```csharp
public string Encrypt(string plainText, int key)
{
    int rows = key;
    int columns = (int)Math.Ceiling((double)plainText.Length / key);
    char[,] ciphermap = new char[rows, columns];
    int charCount = 0;
    for (int c = 0; c < columns; c++)
    {
        for (int r = 0; r < rows; r++)
        {
            if (charCount < plainText.Length)
            {
                ciphermap[r, c] = plainText[charCount];
                charCount++;

            }
            else { break; }
        }
    }

    string finalRes = "";
    for (int i = 0; i < rows; i++)
    {
        for (int j = 0; j < columns; j++)
        {
            finalRes += ciphermap[i, j];
        }
    }

    finalRes=new string(finalRes.Where(char.IsLetter).ToArray());

    return finalRes.ToLower();
  
}
```

### 2. Decryption Algorithm

```csharp
ublic string Decrypt(string cipherText, int key)
{

    int rows = key;
    int columns = (int)Math.Ceiling((double)cipherText.Length / key);
    char[,] plainmap = new char[rows, columns];
    int charCount = 0;
    for (int i = 0; i < rows; i++)
    {
        for (int c = 0; c < columns; c++)
        {
            if (charCount < cipherText.Length)
            {

                plainmap[i, c] = cipherText[charCount];
                charCount++;
            }
        }
    }
    string res = "";
    charCount = 0;
    for (int i = 0; i < columns; i++)
    {
        for (int r = 0; r < rows; r++)
        {
            if (charCount < cipherText.Length)
            {
                res += plainmap[r, i];
            }
        }
    }

    res = new string(res.Where(char.IsLetter).ToArray());
    return res.ToLower();
}
```

### 3. Analysis

```csharp
public int Analyse(string plainText, string cipherText)
{
    string retrunedCipher;
    int key=0;
    for (int i = 1; i < plainText.Length; i++)
    {
        retrunedCipher = Encrypt(plainText, i);
        if (cipherText.Equals(retrunedCipher, StringComparison.OrdinalIgnoreCase))
        {
            key = i;
            break;
        }
    }
    return key;
}
```
