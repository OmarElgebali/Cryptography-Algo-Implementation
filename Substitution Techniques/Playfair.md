# Playfair Technique

### Test Cases
```csharp
string mainPlain = "comsecmeanscommunicationssecurity";
string mainPlain2 = "comsecmeanscommunjcatjonssecurjty";
string mainKey = "galois";
string mainCipher = "dlfdsdndihbddtntuebluoimcvbserulyo".ToUpper();
string mainCipher2 = "dlfdsdndjhbddtntuebluojmcvbserulyo".ToUpper();
```

### 1. Encrypt Algorithm
```csharp
public string Playfair_Encrypt(string plainText, string key)
{
    // create matrix 5x5 to put in it the key and remaining letters
    char[,] playfairMat = new char[5, 5];
    string mat = "";
    int current_indx = 0;
    foreach (char c in key)
    {
        if (mat.Contains(c))
        {
            continue;
        }
        mat += c;
        playfairMat[current_indx / 5 % 5, current_indx % 5] = c;
        current_indx++;
    }
  
    for (int ascii = 97; ascii <= 122; ascii++)
    {
        char c = (char)ascii;
        if (mat.Contains(c) || ascii == 106)
        {
            continue;
        }
        mat += c;
        playfairMat[current_indx / 5 % 5, current_indx % 5] = c;
        current_indx++;
    }
  
    // Display the Matrix
    //for (int i = 0; i < 5; i++)
    //{
    //    for (int j = 0; j < 5; j++)
    //    {
    //        Console.Write($"{playfairMat[i, j]} ");
    //    }
    //    Console.WriteLine();
    //}
  
    // Check if any characters are repeated in the plainText.
    string newPlainText = plainText;
    for (int i = 0; i < newPlainText.Length - 1; i += 2)
    {
        if (newPlainText[i] == newPlainText[i + 1])
        {
            newPlainText = newPlainText.Insert(i + 1, "x");
        }
    }
    if (newPlainText.Length % 2 != 0)
        newPlainText += "x";
    //Console.WriteLine($"newPlainText: {newPlainText}");
  
    // convert the plainText into char array
    char[] twoChar = new char[newPlainText.Length];
    int curr_idx = 0;
    foreach (var c in newPlainText)
    {
        twoChar[curr_idx] = c;
        curr_idx++;
    }
  
    //foreach (var c in twoChar)
    //{
    //    Console.Write($"{c} ");
    //}
    //Console.WriteLine();
  
    // get the index of each char of plainText in playfairMat and get the cipherText
    string cipherText = "";
    int row1, row2, col1, col2;
    for (int i = 0; i < newPlainText.Length; i += 2)
    {
        row1 = GetRowIndex(twoChar[i]);
        //Console.WriteLine($"Row1: {row1}, Char: {twoChar[i]}");
        col1 = GetColIndex(twoChar[i]);
        //Console.WriteLine($"Col1: {col1}, Char: {twoChar[i]}");
  
        row2 = GetRowIndex(twoChar[i + 1]);
        //Console.WriteLine($"Row2: {row2}, Char: {twoChar[i + 1]}");
        col2 = GetColIndex(twoChar[i + 1]);
        //Console.WriteLine($"Col2: {col2}, Char: {twoChar[i + 1]}");
  
        if (row1 == row2)
        {
            cipherText += playfairMat[row1, (col1 + 1) % 5];
            cipherText += playfairMat[row1, (col2 + 1) % 5];
            //Console.WriteLine($" First Case: {cipherText}");
        }
        else if (col1 == col2)
        {
            cipherText += playfairMat[(row1 + 1) % 5, col1];
            cipherText += playfairMat[(row2 + 1) % 5, col1];
            //Console.WriteLine($" Second Case: {cipherText}");
        }
        else
        {
            cipherText += playfairMat[row1, col2];
            cipherText += playfairMat[row2, col1];
            //Console.WriteLine($" Last Case: {cipherText}");
        }
    }
    //Console.WriteLine('\n');
    //Console.WriteLine($"CipherText: {cipherText}");
  
    return cipherText.ToUpper();
  
    int GetRowIndex(char ch)
    {
        for (int i = 0; i < 5; i++)
        {
            for (int j = 0; j < 5; j++)
            {
                if (playfairMat[i, j] == ch)
                {
                    return i;
                }
            }
        }
        return -1;
    }
  
    int GetColIndex(char ch)
    {
        for (int i = 0; i < 5; i++)
        {
            for (int j = 0; j < 5; j++)
            {
                if (playfairMat[i, j] == ch)
                {
                    return j;
                }
            }
        }
        return -1;
    }
}
```


### 2. Decryption Algorithm

```csharp
public string Playfair_Decrypt(string cipherText, string key)
{
    cipherText = cipherText.Replace('j', 'i');
    key = key.Replace('j', 'i');
  
    string plainText = "";
    string mat = "";
    char[,] playfairMat = new char[5, 5];
    int current_index = 0;
    foreach (var c in key)
    {
        if (mat.Contains(c))
        {
            continue;
        }
        mat += c;
        playfairMat[(current_index / 5) % 5, current_index % 5] = c;
        current_index++;
    }
  
    /* a:97 -> z:122 */
    /* A:65 -> Z:90  */
    for (int ascii = 97; ascii <= 122; ascii++)
    {
        char c = (char)ascii;
        if (mat.Contains(c) || ascii == 106)
        {
            continue;
        }
        mat += c;
        playfairMat[(current_index / 5) % 5, current_index % 5] = c;
        current_index++;
    }
  
    //for (int i = 0; i < 5; i++)
    //{
    //    for (int j = 0; j < 5; j++)
    //    {
    //        Console.Write($"{playfairMat[i, j]} ");
    //    }
    //    Console.WriteLine();
    //}
  
    string cipherTemp = cipherText.ToLower();
    //Console.WriteLine(cipherTemp);
  
    int char_index = 0;
    while (char_index < cipherTemp.Length)
    {
        for (; char_index < cipherTemp.Length; char_index += 2)
        {
            if (char_index + 1 == cipherTemp.Length)
            {
                cipherTemp += "x";
            }
            else if (cipherTemp[char_index] == cipherTemp[char_index + 1])
            {
                string part1 = cipherTemp.Substring(0, char_index + 1);
                string part2 = cipherTemp.Substring(char_index + 1);
                cipherTemp = part1 + "x" + part2;
            }
            //Console.WriteLine($"(i:{char_index},{char_index+1}) -> (v:{cipherTemp[char_index]}{cipherTemp[char_index+1]})");
        }
    }
    //Console.WriteLine(cipherTemp);
  
    char_index = 0;
    //Console.WriteLine(cipherTemp.ToUpper());
    while (char_index < cipherTemp.Length)
    {
        for (; char_index < cipherTemp.Length; char_index += 2)
        {
            //Console.WriteLine($"(i:{char_index},{char_index + 1}) -> (v:{cipherTemp[char_index]}{cipherTemp[char_index + 1]})");
            int index_1 = mat.IndexOf(cipherTemp[char_index]);
            int index_2 = mat.IndexOf(cipherTemp[char_index + 1]);
            int index_1_row = (index_1 / 5) % 5, index_1_col = index_1 % 5;
            int index_2_row = (index_2 / 5) % 5, index_2_col = index_2 % 5;
            //Console.WriteLine($"[{index_1_row},{index_1_col}] = {playfairMat[index_1_row, index_1_col]}");
            //Console.WriteLine($"[{index_2_row},{index_2_col}] = {playfairMat[index_2_row, index_2_col]}");
            if (index_1_row == index_2_row)
            {
                index_1_col = (index_1_col - 1) % 5 < 0 ? 4 : (index_1_col - 1) % 5;
                index_2_col = (index_2_col - 1) % 5 < 0 ? 4 : (index_2_col - 1) % 5;
            }
            else if (index_1_col == index_2_col)
            {
                index_1_row = (index_1_row - 1) % 5 < 0 ? 4 : (index_1_row - 1) % 5;
                index_2_row = (index_2_row - 1) % 5 < 0 ? 4 : (index_2_row - 1) % 5;
            }
            else
            {
                int temp_col = index_1_col;
                index_1_col = index_2_col;
                index_2_col = temp_col;
            }
            //Console.Write($"[{index_1_row},{index_1_col}] = ");
            //Console.WriteLine($"{playfairMat[index_1_row, index_1_col]}");
            //Console.Write($"[{index_2_row},{index_2_col}] = ");
            //Console.WriteLine($"{playfairMat[index_2_row, index_2_col]}");
            plainText += $"{playfairMat[index_1_row, index_1_col]}{playfairMat[index_2_row, index_2_col]}";
        }
    }
    //Console.WriteLine(plainText);
  
  
    plainText = plainText[plainText.Length - 1] == 'x' ? plainText.Substring(0, plainText.Length - 1) : plainText;
  
    for (char_index = 0; char_index < plainText.Length; char_index += 2)
    {
        if (char_index + 2 >= plainText.Length)
            continue;
        if (plainText[char_index] == plainText[char_index + 2] && plainText[char_index + 1] == 'x')
        {
            string part1 = plainText.Substring(0, char_index + 1);
            string temp = plainText.Substring(char_index + 1);
            string consder_x = temp.Substring(0, 1);
            string part2 = temp.Substring(1);
            plainText = part1 + part2;
            char_index--;
        }
    }
  
    //Console.WriteLine(plainText);
    //Console.WriteLine(mat);
    //Console.WriteLine(mat.Length);
    //Console.WriteLine("*********************************************************************");
  
    return plainText;
}

```