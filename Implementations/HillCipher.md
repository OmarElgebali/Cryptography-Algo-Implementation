# Hill Cipher Technique

### Test Cases

```csharp
// Pass Test Cases
string mainKey = "dcif";
string mainPlain = "paymoremoney";
string mainCipher = "tqssypkoqviw".ToUpper();

List<int> key = new List<int>() { 3, 2, 8, 5 };
List<int> plain = new List<int>() { 15, 0, 24, 12, 14, 17, 4, 12, 14, 13, 4, 24 };
List<int> cipher = new List<int>() { 19, 16, 18, 18, 24, 15, 10, 14, 16, 21, 8, 22 };

string mainKey1 = "bkaaubcpc";
string mainPlain1 = "fvcfcqtob";
string mainCipher1 = "hgrzeudvq".ToUpper();

List<int> key1 = new List<int> { 1, 10, 0, 0, 20, 1, 2, 15, 2 };
List<int> plain1 = new List<int> { 5, 21, 2, 5, 2, 16, 19, 14, 1 };
List<int> cipher1 = new List<int> { 7, 6, 17, 25, 4, 20, 3, 21, 16 };

// Error Test Cases
string mainPlainError = "lkdi";
string mainCipherError = "SDEK".ToUpper();

List<int> mainPlainError2 = new List<int>() { 11, 10, 3, 8 };
List<int> mainCipherError2 = new List<int>() { 18, 3, 4, 10 };

```
### 1. Encrypt Algorithm
```csharp
public List<int> Encrypt(List<int> plainText, List<int> key)
{
    int keyDiminsion = (key.Count % 2) + 2;
    List<int> result = new List<int>();
    int reminder = plainText.Count() % keyDiminsion;

    for (int i = 0; i < reminder; i++)
    {
        plainText.Add(23);
    }

    for (int i = 0; i < plainText.Count; i += keyDiminsion)//3 by 3  or  2 by 2
    {
        int sum = 0;
        int diminsionCounter = 0;
        for (int j = 0; j < key.Count; j++)
        {
            //here happens the 3 or 2 multiplications
            sum += key[j] * plainText[i + diminsionCounter];
            diminsionCounter++;

            if (diminsionCounter == keyDiminsion)
            {
                result.Add(sum % 26);
                sum = 0;
                diminsionCounter = 0;
            }
        }
    }
    return result;
}

public string Encrypt(string plainText, string key)
{

    List<int> plainNumbers = new List<int>();
    List<int> keyNumbers = new List<int>();
    List<int> resultCipherNumbers = new List<int>();
    string resultInString = "";
    foreach (var c in plainText)
    {
        plainNumbers.Add(Convert.ToInt32(c) - 97);
    }

    foreach (var k in key)
    {
        keyNumbers.Add(Convert.ToInt32(k) - 97);
    }

    resultCipherNumbers = Encrypt(plainNumbers, keyNumbers);
    foreach (var cipher in resultCipherNumbers)
    {

        resultInString += Convert.ToChar(cipher + 65);
    }
    return resultInString;
}
```

### 2. Decryption Algorithm

```csharp
public List<int> Decrypt(List<int> cipherText, List<int> key)
{
    List<int> keyInversed = new List<int>();
    int keyDiminsion = (key.Count % 2) + 2;

    if (keyDiminsion == 2)
    {

        int determinant2 = (key[0] * key[3]) - (key[1] * key[2]);
        int gcd2 = 1;
        for (int i = 1; i <= 26 && i <= determinant2; ++i)
        {
            if (26 % i == 0 && determinant2 % i == 0)
                gcd2 = i;
        }
        if (gcd2 > 1 || determinant2 == 0)
        {
           Console.log("InvalidAnlysisException");
        }
        else
        {
            int temp0 = (1 / determinant2) * key[3];
            keyInversed.Add(((temp0 % 26) + 26) % 26);

            int temp1 = (1 / determinant2) * key[1] * -1;
            keyInversed.Add(((temp1 % 26) + 26) % 26);

            int temp2 = (1 / determinant2) * key[2] * -1;
            keyInversed.Add(((temp2 % 26) + 26) % 26);

            int temp3 = (1 / determinant2) * key[0];
            keyInversed.Add(((temp3 % 26) + 26) % 26);
        }
        return Encrypt(cipherText, keyInversed);
    }

    int[,] OriginalKey = new int[3, 3];
    int[,] InvertedKey = new int[3, 3];
    List<int> TransposedKey = new List<int>();
    for (int i = 0; i <= 6; i += 3)
    {
        for (int j = 0; j < 3; j++)
        {
            OriginalKey[i / 3, j] = key[i + j];
        }
    }

    int determinant =
            key[0] * (key[4] * key[8] - key[5] * key[7])
        - key[1] * (key[3] * key[8] - key[5] * key[6])
        + key[2] * (key[3] * key[7] - key[4] * key[6]);

    int gcd = 1;
    for (int i = 1; i <= 26 && i <= determinant; ++i)
    {
        if (26 % i == 0 && determinant % i == 0)
            gcd = i;
    }
    if (gcd > 1)
    {
        Console.log("InvalidAnlysisException");
    }
    else
    {
        int x = 26 - determinant;
        int c = 1;
        for (; c % x != 0; c += 26) ;
        int B = 26 - c / x;

        for (int y = 0; y < 3; y++)
        {
            for (int i = 0; i < 3; i++)
            {
                List<int> detMat = new List<int>();
                for (int q = 0; q < key.Count; q++)
                {
                    if (q % 3 != i)
                    {
                        if (q / 3 != y)
                        {
                            detMat.Add(key[q]);
                        }
                    }
                }
                int det = (detMat[0] * detMat[3]) - (detMat[1] * detMat[2]);
                InvertedKey[y, i] = ((B * power(-1, i + y) * det) % 26 + 26) % 26;
            }
        }
        for (int y = 0; y < 3; y++)
        {
            for (int i = 0; i < 3; i++)
            {
                TransposedKey.Add(InvertedKey[i, y]);
            }
        }
    }
    return Encrypt(cipherText, TransposedKey);
}

public string Decrypt(string cipherText, string key)
{
    List<int> cipherNumbers = new List<int>();
    List<int> keyNumbers = new List<int>();
    List<int> resultPlainNumbers = new List<int>();
    string resultInString = "";
    
    foreach (var c in cipherText)
    {
        cipherNumbers.Add(Convert.ToInt32(c) - 65);
    }
    foreach (var k in key)
    {
        keyNumbers.Add(Convert.ToInt32(k) - 65);
    }

    resultPlainNumbers = Decrypt(cipherNumbers, keyNumbers);
    foreach (var p in resultPlainNumbers)
    {

        resultInString += Convert.ToChar(p + 97);
    }
    return resultInString;
}
```

### 3. Analysis 2x2

```csharp
public List<int> Analyse(List<int> plainText, List<int> cipherText)
{
    int p1 = plainText[0], p2 = plainText[1], p3 = plainText[2], p4 = plainText[3];
    int c1 = cipherText[0], c2 = cipherText[1], c3 = cipherText[2], c4 = cipherText[3];
    int modulus = 26;

    for (int a = 0; a < modulus; a++)
    {
        for (int b = 0; b < modulus; b++)
        {
            for (int c = 0; c < modulus; c++)
            {
                for (int d = 0; d < modulus; d++)
                {
                    if (((a * p1 + b * p2) % 26 + 26) % 26 == c1 &&
                        ((c * p1 + d * p2) % 26 + 26) % 26 == c2 &&
                        ((a * p3 + b * p4) % 26 + 26) % 26 == c3 &&
                        ((c * p3 + d * p4) % 26 + 26) % 26 == c4)
                    {
                        Console.WriteLine("a = " + a);
                        Console.WriteLine("b = " + b);
                        Console.WriteLine("c = " + c);
                        Console.WriteLine("d = " + d);
                        List<int> solution = new List<int>();
                        solution.Add(a);
                        solution.Add(b);
                        solution.Add(c);
                        solution.Add(d);
                        return solution;
                    }
                }
            }
        }
    }

   Console.log("InvalidAnlysisException");
}

public string Analyse(string plainText, string cipherText)
{
    List<int> cipherNumbers = new List<int>();
    List<int> PlainNumbers = new List<int>();
    List<int> resultkeyNumbers = new List<int>();
    string resultInString = "";
    
    foreach (var c in cipherText)
    {
        cipherNumbers.Add(Convert.ToInt32(c) - 65);
    }
    foreach (var k in key)
    {
        PlainNumbers.Add(Convert.ToInt32(k) - 97);
    }

    resultkeyNumbers = Analyse(cipherNumbers, PlainNumbers);
    foreach (var p in resultkeyNumbers)
    {
        resultInString += Convert.ToChar(p + 65);
    }
    return resultInString;
}
```
### 4. Analysis 3x3
```csharp
public List<int> Analyse3By3Key(List<int> plainText, List<int> cipherText)
{
    List<int> Key = new List<int>();
    int[,] OriginalPlain = new int[3, 3];
    int[,] InvertedPlain = new int[3, 3];
    List<int> TransposedPlain = new List<int>();
    for (int i = 0; i <= 6; i += 3)
    {
        for (int j = 0; j < 3; j++)
        {
            OriginalPlain[i / 3, j] = plainText[i + j];
        }
    }

    int determinant =
            plainText[0] * (plainText[4] * plainText[8] - plainText[5] * plainText[7])
        - plainText[1] * (plainText[3] * plainText[8] - plainText[5] * plainText[6])
        + plainText[2] * (plainText[3] * plainText[7] - plainText[4] * plainText[6]);

    int gcd = 1;
    for (int i = 1; i <= 26 && i <= determinant; ++i)
    {
        if (26 % i == 0 && determinant % i == 0)
            gcd = i;
    }
    if (gcd > 1)
    {
       Console.log("InvalidAnlysisException");
    }

    int x = 26 - determinant;
    int c = 1;
    for (; c % x != 0; c += 26) ;
    int B = 26 - c / x;

    for (int y = 0; y < 3; y++)
    {

        for (int i = 0; i < 3; i++)
        {
            List<int> detMat = new List<int>();
            for (int q = 0; q < plainText.Count; q++)
            {
                if (q % 3 != i)
                {
                    if (q / 3 != y)
                    {
                        detMat.Add(plainText[q]);
                    }
                }
            }
            int det = (detMat[0] * detMat[3]) - (detMat[1] * detMat[2]);
            InvertedPlain[y, i] = ((B * power(-1, i + y) * det) % 26 + 26) % 26;

        }
    }
    for (int y = 0; y < 3; y++)
    {

        for (int i = 0; i < 3; i++)
        {
            TransposedPlain.Add(InvertedPlain[i, y]);
        }
    }

    List<List<int>> C = ConvertTo2DMatrix(cipherText, 3, 3);
    List<List<int>> P = ConvertTo2DMatrix(TransposedPlain, 3, 3);

    List<List<int>> MulMat = MultiplyMatrices(P, C);
    Console.WriteLine("Matrix C (Result of A * B):");
    List<int> Result = FlattenAndTranspose(MulMat);
    
    PrintMatrix(MulMat);

    return Result;
}

public string Analyse3By3Key(string plain3, string cipher3)
{
    List<int> cipherNumbers = new List<int>();
    List<int> PlainNumbers = new List<int>();
    List<int> resultkeyNumbers = new List<int>();
    string resultInString = "";
    
    foreach (var c in cipherText)
    {
        cipherNumbers.Add(Convert.ToInt32(c) - 65);
    }
    foreach (var k in key)
    {
        PlainNumbers.Add(Convert.ToInt32(k) - 97);
    }

    resultkeyNumbers = Analyse3By3Key(cipherNumbers, PlainNumbers);
    foreach (var p in resultkeyNumbers)
    {
        resultInString += Convert.ToChar(p + 65);
    }
    return resultInString;
}

public int power(int b, int n)
{
    int cummulative_product = 1;
    for (int i = 1; i <= n; i++)
    {
        cummulative_product *= b;
    }
    return cummulative_product;
}
static List<List<int>> MultiplyMatrices(List<List<int>> A, List<List<int>> B)
{
    int rowsA = A.Count;
    int colsA = A[0].Count;
    int rowsB = B.Count;
    int colsB = B[0].Count;

    // Check if the matrices are compatible for multiplication
    if (colsA != rowsB)
    {
        throw new ArgumentException("Number of columns in A must be equal to the number of rows in B.");
    }

    // Initialize result matrix C
    List<List<int>> C = new List<List<int>>();
    for (int i = 0; i < rowsA; i++)
    {
        C.Add(new List<int>());
        for (int j = 0; j < colsB; j++)
        {
            C[i].Add(0);
        }
    }

    // Perform matrix multiplication
    for (int i = 0; i < rowsA; i++)
    {
        for (int j = 0; j < colsB; j++)
        {
            for (int k = 0; k < colsA; k++)
            {
                C[i][j] += A[i][k] * B[k][j];
            }
            C[i][j] = C[i][j] % 26;
        }
    }

    return C;
}

static List<List<int>> ConvertTo2DMatrix(List<int> flatList, int rows, int cols)
{
    List<List<int>> matrix = new List<List<int>>();
    int index = 0;
    for (int i = 0; i < rows; i++)
    {
        matrix.Add(new List<int>());
        for (int j = 0; j < cols; j++)
        {
            matrix[i].Add(flatList[index]);
            index++;
        }
    }
    return matrix;
}

static void PrintMatrix(List<List<int>> matrix)
{
    int rows = matrix.Count;
    int cols = matrix[0].Count;

    for (int i = 0; i < rows; i++)
    {
        for (int j = 0; j < cols; j++)
        {
            Console.Write(matrix[i][j] + " ");
        }
        Console.WriteLine();
    }
}
static List<int> FlattenAndTranspose(List<List<int>> matrix)
{
    List<int> flattenedList = new List<int>();
    foreach (List<int> row in matrix)
    {
        flattenedList.AddRange(row);
    }

    // Transpose the 1D list
    List<int> transposedList = new List<int>();
    int numRows = matrix.Count;
    int numCols = matrix[0].Count;
    for (int j = 0; j < numCols; j++)
    {
        for (int i = 0; i < numRows; i++)
        {
            transposedList.Add(flattenedList[i * numCols + j]);
        }
    }

    return transposedList;
}

static int extended_euclidean(int b, int m)
{
    int Q;
    int[] A = { 1, 0, m };
    int[] B = { 0, 1, b };
    while (B[2] > 1)
    {
        Q = A[2] / B[2];
        int[] tempB = new int[3];
        for (int i = 0; i < 3; i++)
        {
            tempB[i] = A[i] - Q * B[i];
        }
        for (int i = 0; i < 3; i++)
        {
            A[i] = B[i];
            B[i] = tempB[i];
        }
    }

    //return ((B[2] == 1 ? B[1] : A[2]) % 26 + 26) % 26;
    return B[2] == 1 ? B[1] : A[2];
}
```