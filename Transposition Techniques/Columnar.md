# Columnar Technique

### Test Cases

```csharp
List<int> mainkey1 = new List<int>() { 4, 3, 1, 2, 5, 6, 7 };
string mainPlain1_1 = "attackpostponeduntiltwoam";
string mainPlain1_2 = "attackpostponeduntiltwoamxxx";
string mainCipher1_1 = "ttnaaptmtsuoaodwcoiknlpet".ToUpper();
string mainCipher1_2 = "ttnaaptmtsuoaodwcoixknlxpetx".ToUpper();

List<int> mainkey2 = new List<int>() { 1, 3, 4, 2, 5 };
string mainPlain2 = "computerscience";
string mainCipher2 = "ctipscoeemrnuce".ToUpper();

List<int> mainkey3 = new List<int>() { 1, 4, 3, 2 };
string mainPlain3_1 = "computerscience";
string mainPlain3_2 = "computersciencex";
string mainCipher3_1 = "cusnpremeieotcc".ToUpper();
string mainCipher3_2 = "cusnprexmeieotcc".ToUpper();

List<int> mainkey4 = new List<int>() { 3, 2, 6, 4, 1, 5 };
string mainPlain4 = "defendtheeastwallofthecastleee";
string mainCipher4 = "nalceehwttdttfseeleedsoaefeahl";
```

### 1. Encrypt Algorithm

```csharp
public string Encrypt(string plainText, List<int> key)
{
    int number_of_columns = key.Max();
    string[] mat = new string[number_of_columns];
    int number_of_original_letters = plainText.Length;
    int remaining_letters = (number_of_columns - (number_of_original_letters % number_of_columns)) % number_of_columns;
    int number_of_letters = number_of_original_letters + remaining_letters;
    
    //Console.WriteLine($"number_of_original_letters: {number_of_original_letters}");
    //Console.WriteLine($"number_of_columns:          {number_of_columns}");
    //Console.WriteLine($"remaining_letters:          {remaining_letters}");
    //Console.WriteLine($"number_of_letters:          {number_of_letters}");

    //Console.WriteLine(plainText);
    if (remaining_letters != 0)
    {
        plainText += new string('x', remaining_letters);
    }
    //Console.WriteLine(plainText);

    for (int i = 0; i < number_of_columns; i++)
        for (int j = i; j < number_of_letters; j += number_of_columns)
            mat[i] += plainText[j];

    //for (int i = 0; i < number_of_columns; i++)
    //    Console.WriteLine($"{i}: {mat[i]} ");

    string cipher = "";
    for (int i = 1; i <= number_of_columns; i++)
    {
        cipher += mat[key.IndexOf(i)].ToUpper();
    }
    //Console.WriteLine(cipher);
    //Console.WriteLine(cipher.Length);

    return cipher;
}
```

### 2. Decryption Algorithm

```csharp
public string Decrypt(string cipherText, List<int> key)
{
    int number_of_columns = key.Max();
    string[] mat = new string[number_of_columns];
    int number_of_letters = cipherText.Length;
    int number_of_rows = number_of_letters / number_of_columns;

    //Console.WriteLine($"number_of_letters:  {number_of_letters}");
    //Console.WriteLine($"number_of_columns:  {number_of_columns}");
    //Console.WriteLine($"number_of_rows:     {number_of_rows}");

    //Console.WriteLine(cipherText);

    for (int i = 0; i < number_of_columns; i++)
        for (int j = (i * number_of_rows); j < ((i * number_of_rows) + number_of_rows); j++)
            mat[i] += cipherText[j];

    //for (int i = 0; i < number_of_columns; i++)
    //    Console.Write($"{mat[i]} ");
    //Console.WriteLine();

    //for (int i = 0; i < number_of_columns; i++)
    //    Console.WriteLine($"{i}: {mat[i]} ");
    //Console.WriteLine();

    string[] indexedMat = new string[number_of_columns];
    int index = 0;
    foreach (int i in key)
    {
        indexedMat[index++] += mat[i - 1].ToLower();
    }

    //for (int i = 0; i < number_of_columns; i++)
    //    Console.WriteLine($"{i}: {indexedMat[i]} ");

    string plainText = "";
    for (int i = 0; i < number_of_rows; i++)
        for (int j = 0; j < number_of_columns; j++)
            plainText += indexedMat[j][i];

    //Console.WriteLine(plainText);
    //Console.WriteLine(plainText.Length);

    return plainText;
}
```

### 3. Analysis

```csharp
public List<int> Analyse(string plainText, string cipherText)
{
    List<int> key = new List<int>();

    string cipherTextLowered = cipherText.ToLower();
    //Console.WriteLine($"plainText:  {plainText}");
    //Console.WriteLine($"cipherText: {cipherTextLowered}");

    int number_of_letters = cipherTextLowered.Length;
    //Console.WriteLine($"number_of_letters:  {number_of_letters}");

    List<int> possible_key_legnths = new List<int>();
    for (int i = 2; i < (number_of_letters / 2); i++)
    {
        if (number_of_letters % i == 0 && !possible_key_legnths.Contains(i))
        {
            possible_key_legnths.Add(i);
            possible_key_legnths.Add(number_of_letters / i);
        }
    }

    List<int> possible_indices_of_first_char = new List<int>();
    List<int> possible_indices_of_second_char = new List<int>();

    for (int i = 0; i < number_of_letters; i++)
    {
        if (plainText[i] == cipherTextLowered[0])
        {
            possible_indices_of_first_char.Add(i);
        }

        if (plainText[i] == cipherTextLowered[1])
        {
            possible_indices_of_second_char.Add(i);
        }
    }

    //Console.Write($"1st Char ({cipherTextLowered[0]}) Indicies {possible_indices_of_first_char.Count}: ");
    //foreach (int index in possible_indices_of_first_char)
    //    Console.Write($"{index} ");
    //Console.WriteLine();

    //Console.Write($"2nd Char ({cipherTextLowered[1]}) Indicies {possible_indices_of_second_char.Count}: ");
    //foreach (int index in possible_indices_of_second_char)
    //    Console.Write($"{index} ");
    //Console.WriteLine();

    //Console.Write($"possible_key_legnths: ");
    //foreach (int l in possible_key_legnths)
    //    Console.Write($"{l} ");
    //Console.WriteLine();

    int number_of_columns = -1;
    foreach (int second_index in possible_indices_of_second_char)
        foreach (var first_index in possible_indices_of_first_char)
            if (number_of_columns == -1 && possible_key_legnths.Contains(second_index - first_index))
                number_of_columns = second_index - first_index;

    //Console.WriteLine($"number_of_letters:  {number_of_letters}");
    //Console.WriteLine($"number_of_columns:  {number_of_columns}");

    int number_of_rows = number_of_letters / number_of_columns;
    //Console.WriteLine($"number_of_rows:     {number_of_rows}");

    if (number_of_columns == -1)
        return Enumerable.Repeat(default(int), number_of_letters).ToList();

    string[] mat = new string[number_of_columns];

    for (int i = 0; i < number_of_columns; i++)
        for (int j = i; j < number_of_letters; j += number_of_columns)
            mat[i] += plainText[j];

    for (int i = 0; i < number_of_columns; i++)
        Console.Write($"{mat[i]} ");
    Console.WriteLine();

    for (int i = 0; i < number_of_columns; i++)
        Console.WriteLine($"{i}: {mat[i]} ");

    foreach (string chunk in mat)
    {
        Console.Write($"{(cipherTextLowered.IndexOf(chunk) / number_of_rows) + 1} ");
        key.Add((cipherTextLowered.IndexOf(chunk) / number_of_rows) + 1);
    }
    Console.WriteLine();

    return key;
}
```