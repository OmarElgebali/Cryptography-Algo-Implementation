# Data Encryption Standard (DES)

### Test Cases

```csharp
// Test 1
string mainPlain = "0x0123456789ABCDEF";
string mainCipher = "0x85E813540F0AB405";
string mainKey = "0x133457799BBCDFF1";

string mainPlain2 = "0x596F7572206C6970";
string mainCipher2 = "0xC0999FDDE378D7ED";
string mainKey2 = "0x0E329232EA6D0D73";

string mainPlain3 = "0x6D6573736167652E";
string mainCipher3 = "0x7CF45E129445D451";
string mainKey3 = "0x38627974656B6579";

```

### 1. Encode Mapping (Hexa ↔ Digit)

```csharp
static Dictionary<char, string> encode_mapping = new Dictionary<char, string>();

static string FindKeyByValue(string value)
{
    foreach (var pair in encode_mapping)
        if (pair.Value == value)
            return pair.Key.ToString();
    return "!";
}

static void init_encode_map()
{
    encode_mapping.Clear();
    encode_mapping.Add('0', "0000");
    encode_mapping.Add('1', "0001");
    encode_mapping.Add('2', "0010");
    encode_mapping.Add('3', "0011");
    encode_mapping.Add('4', "0100");
    encode_mapping.Add('5', "0101");
    encode_mapping.Add('6', "0110");
    encode_mapping.Add('7', "0111");
    encode_mapping.Add('8', "1000");
    encode_mapping.Add('9', "1001");
    encode_mapping.Add('A', "1010");
    encode_mapping.Add('B', "1011");
    encode_mapping.Add('C', "1100");
    encode_mapping.Add('D', "1101");
    encode_mapping.Add('E', "1110");
    encode_mapping.Add('F', "1111");
}

static string HexWord_to_BinWord(string hex)
{
    string resBin = "";
    foreach (char hexNum in hex)
    {
        resBin += encode_mapping[hexNum];
    }
    return resBin;
}

static string BinWord_to_HexWord(string binary)
{
    string resHex = "";
    for (int i = 0; i < binary.Length; i += 4)
    {
        resHex += FindKeyByValue(binary.Substring(i, 4));
    }
    return resHex;
}
```

### 2. Helpers

1. XOR operations
2. Split binary list into 2 halves
3. Print binary list
4. Convert Decimal Integer to 4-Bit string

```csharp
static string XOR(List<string> binaryString)
{
    string output = "";
    for (int i = 0; i < binaryString[0].Length; i++)
    {
        int col_sum = 0;
        for (int j = 0; j < binaryString.Count; j++)
        {
            col_sum += (binaryString[j][i] == '1') ? 1 : 0;
        }
        output += (col_sum % 2 != 0) ? '1' : '0';
    }
    return output;
}

public static string[] splitPlainTextTo2Strings(string plainTextBinary)
{
    string plainTextLeftSide = "";
    string plainTextRightSide = "";
    plainTextLeftSide = plainTextBinary.Substring(0, plainTextBinary.Length/2);
    plainTextRightSide = plainTextBinary.Substring(plainTextBinary.Length / 2, plainTextBinary.Length / 2);
    string[] splitedPlainStrings = new string[2];

    splitedPlainStrings[0] = plainTextLeftSide;
    splitedPlainStrings[1] = plainTextRightSide;

    return splitedPlainStrings;
}

static void print_Bin(string message, string binary, int sector_length)
{
    Console.Write($"{message}: ");
    for (int i = 0; i < binary.Length; i += sector_length)
    {
        Console.Write($"{binary.Substring(i, sector_length)} ");
    }
    Console.WriteLine();
}

public static string IntegerToBinary_4bits(int number)
{
    string binaryString = Convert.ToString(number, 2);
    int len = binaryString.Length;
    int desiredLen = 4 - len; // must be 0 to ensure that all numbers in 4-bits
    if (desiredLen > 0)
    {
        for (int i = 0; i < desiredLen; i++)
        {
            binaryString = "0" + binaryString;
        }
    }
    return binaryString;
}
```

### 3. Key Generation

```csharp
static string PC_1(string binkey)
{
    int[] pc_1 = new int[56] { 57, 49, 41, 33, 25, 17, 9 ,
                                1 , 58, 50, 42, 34, 26, 18,
                                10, 2 , 59, 51, 43, 35, 27,
                                19, 11, 3 , 60, 52, 44, 36,
                                63, 55, 47, 39, 31, 23, 15,
                                7 , 62, 56, 46, 38, 30, 22,
                                14, 6 , 61, 53, 45, 37, 29,
                                21, 13, 5 , 28, 20, 12, 4 
                                };
    string pc_key = "";
    for (int i = 0; i < pc_1.Length; i++)
    {
        pc_key += binkey[pc_1[i]-1];
    }
    return pc_key;
}

static string[] PC_2(string[] keys)
{
    int[] pc_2 = new int[48] { 14, 17, 11, 24, 1 , 5 ,
                        3 , 28, 15, 6 , 21, 10,
                        23, 19, 12, 4 , 26, 8 ,
                        16, 7 , 27, 20, 13, 2 ,
                        41, 52, 31, 37, 47, 55,
                        30, 40, 51, 45, 33, 48,
                        44, 49, 39, 56, 34, 53,
                        46, 42, 50, 36, 29, 32,
                        };

    string[] Ks = new string[16];
    for (int i = 0; i < keys.Count(); i += 2)
    {
        string pc_key = "";
        for (int j = 0; j < pc_2.Length; j++)
        {
            if (pc_2[j] > 28)
                pc_key += keys[i + 1][pc_2[j] - 28 - 1];
            else
                pc_key += keys[i][pc_2[j] - 1];
        }
        Ks[i / 2] = pc_key;
    }

    return Ks;
}

public static string[] keying(string key)
{
    string PCed_key = PC_1(key);
    string[] CD_Zero = splitPlainTextTo2Strings(PCed_key);
    string No_shift_Lift = "1122222212222221";
    string[] Cs = new string[No_shift_Lift.Length];
    string[] Ds = new string[No_shift_Lift.Length];
    string[] strings = new string[32];

    for (int i = 0; i < No_shift_Lift.Length; i++)
    {
        Cs[i] = CD_Zero[0].Substring(Int32.Parse(No_shift_Lift[i].ToString()));
        Cs[i] += (CD_Zero[0].Substring(0, Int32.Parse(No_shift_Lift[i].ToString())));
        Ds[i] = CD_Zero[1].Substring(Int32.Parse(No_shift_Lift[i].ToString()));
        Ds[i] += (CD_Zero[1].Substring(0, Int32.Parse(No_shift_Lift[i].ToString())));
        CD_Zero[0] = Cs[i];
        CD_Zero[1] = Ds[i];
        strings[2*i] = Cs[i];
        strings[2*i + 1] = Ds[i];
    }
    return strings;

}
```

### 4. Initial Permutation & its inverse (64-Bit)

```csharp
static string IP(string binPlainText, bool inverse=false)
{
    int[] ip = new int[64]
    {
        58, 50, 42, 34, 26, 18, 10, 2,
        60, 52, 44, 36, 28, 20, 12, 4,
        62, 54, 46, 38, 30, 22, 14, 6,
        64, 56, 48, 40, 32, 24, 16, 8,
        57, 49, 41, 33, 25, 17, 9 , 1,
        59, 51, 43, 35, 27, 19, 11, 3,
        61, 53, 45, 37, 29, 21, 13, 5,
        63, 55, 47, 39, 31, 23, 15, 7
    };
    int[] inverseIP = new int[64]
    {
        40, 8, 48, 16, 56, 24, 64, 32,
        39, 7, 47, 15, 55, 23, 63, 31,
        38, 6, 46, 14, 54, 22, 62, 30,
        37, 5, 45, 13, 53, 21, 61, 29,
        36, 4, 44, 12, 52, 20, 60, 28,
        35, 3, 43, 11, 51, 19, 59, 27,
        34, 2, 42, 10, 50, 18, 58, 26,
        33, 1, 41, 9 , 49, 17, 57, 25
    };


    string ip_plainText = "";
    if (inverse==true)
    {
        for (int i = 0; i < ip.Length; i++)
        {
            ip_plainText += binPlainText[inverseIP[i] - 1];
        }
    }
    else
    {
        for (int i = 0; i < ip.Length; i++)
        {
            ip_plainText += binPlainText[ip[i] - 1];
        }
    }
    Console.WriteLine(ip_plainText);

    return ip_plainText;
}

```

### 5. Expansion Table (32-Bit -> 48-Bit)

```csharp
public static string expandString(string splitedPlainText)
{

    int[] expansionTable = {32, 1, 2, 3, 4, 5, 4, 5, 6, 7, 8, 9,8, 9, 10, 11, 12, 13,12,
    13, 14, 15, 16, 17,16, 17, 18, 19, 20, 21,20, 21, 22, 23, 24, 25,24, 25, 26, 27, 28, 29,28, 29, 30, 31, 32, 1};
    string expandedString = "";
    for (int i = 0; i < 48; i++)
    {
        expandedString += splitedPlainText[expansionTable[i] - 1];
    }
    return expandedString;
}
```

### 6. S-Boxes

```csharp
static int[,] S1 = new int[4, 16] {
    {14,  4, 13,  1,  2, 15, 11,  8,  3, 10,  6, 12,  5,  9,  0,  7},
    { 0, 15,  7,  4, 14,  2, 13,  1, 10,  6, 12, 11,  9,  5,  3,  8},
    { 4,  1, 14,  8, 13,  6,  2, 11, 15, 12,  9,  7,  3, 10,  5,  0},
    {15, 12,  8,  2,  4,  9,  1,  7,  5, 11,  3, 14, 10,  0,  6, 13}
};
static int[,] S2 = new int[4, 16] {
    {15,  1,  8, 14,  6, 11,  3,  4,  9,  7,  2, 13, 12,  0,  5, 10},
    { 3, 13,  4,  7, 15,  2,  8, 14, 12,  0,  1, 10,  6,  9, 11,  5},
    { 0, 14,  7, 11, 10,  4, 13,  1,  5,  8, 12,  6,  9,  3,  2, 15},
    {13,  8, 10,  1,  3, 15,  4,  2, 11,  6,  7, 12,  0,  5, 14,  9}
};
static int[,] S3 = new int[4, 16] {
    {10,  0,  9, 14,  6,  3, 15,  5,  1, 13, 12,  7, 11,  4,  2,  8},
    {13,  7,  0,  9,  3,  4,  6, 10,  2,  8,  5, 14, 12, 11, 15,  1},
    {13,  6,  4,  9,  8, 15,  3,  0, 11,  1,  2, 12,  5, 10, 14,  7},
    { 1, 10, 13,  0,  6,  9,  8,  7,  4, 15, 14,  3, 11,  5,  2, 12}
};
static int[,] S4 = new int[4, 16] {
    { 7, 13, 14,  3,  0,  6,  9, 10,  1,  2,  8,  5, 11, 12,  4, 15},
    {13,  8, 11,  5,  6, 15,  0,  3,  4,  7,  2, 12,  1, 10, 14,  9},
    {10,  6,  9,  0, 12, 11,  7, 13, 15,  1,  3, 14,  5,  2,  8,  4},
    { 3, 15,  0,  6, 10,  1, 13,  8,  9,  4,  5, 11, 12,  7,  2, 14}
};
static int[,] S5 = new int[4, 16] {
    {2,  12,  4,  1,  7, 10, 11,  6,  8,  5,  3, 15, 13,  0, 14,  9},
    {14, 11,  2, 12,  4,  7, 13,  1,  5,  0, 15, 10,  3,  9,  8,  6},
    { 4,  2,  1, 11, 10, 13,  7,  8, 15,  9, 12,  5,  6,  3,  0, 14},
    {11,  8, 12,  7,  1, 14,  2, 13,  6, 15,  0,  9, 10,  4,  5,  3}
};
static int[,] S6 = new int[4, 16] {
    {12,  1, 10, 15,  9,  2,  6,  8,   0, 13,  3,  4, 14,  7,  5, 11},
    {10, 15,  4,  2,  7, 12,  9,  5,   6,  1, 13, 14,  0, 11,  3,  8},
    { 9, 14, 15,  5,  2,  8, 12,  3,   7,  0,  4, 10,  1, 13, 11,  6},
    { 4,  3,  2, 12,  9,  5, 15, 10,  11, 14,  1,  7,  6,  0,  8, 13}
};
static int[,] S7 = new int[4, 16] {
    { 4, 11,  2, 14, 15,  0,  8, 13,   3, 12,  9,  7,  5, 10,  6,  1},
    {13,  0, 11,  7,  4,  9,  1, 10,  14,  3,  5, 12,  2, 15,  8,  6},
    { 1,  4, 11, 13, 12,  3,  7, 14,  10, 15,  6,  8,  0,  5,  9,  2},
    { 6, 11, 13,  8,  1,  4, 10,  7,   9,  5,  0, 15, 14,  2,  3, 12}
};
static int[,] S8 = new int[4, 16] {
    {13,  2,  8,  4,  6, 15, 11,  1,  10,  9,  3, 14,  5,  0, 12,  7},
    { 1, 15, 13,  8, 10,  3,  7,  4,  12,  5,  6, 11,  0, 14,  9,  2},
    { 7, 11,  4,  1,  9, 12, 14,  2,   0,  6, 10, 13, 15,  3,  5,  8},
    { 2,  1, 14,  7,  4, 10,  8, 13,  15, 12,  9,  0,  3,  5,  6, 11}
};

static List<int[,]> S_Boxes = new List<int[,]>()
{
    S1,
    S2,
    S3,
    S4,
    S5,
    S6,
    S7,
    S8
};

static string sbox_48_to_32(string input)
{
    string FinalRes = "";
    int start, end;
    for (int i = 0; i < S_Boxes.Count; i++)
    {
        start = (i * 6);
        end = (start + 5);
        string current_string = "";
        for (int j = start; j <= end; j++)
        {
            current_string += input[j];
        }
        string row = $"{current_string[0]}{current_string[current_string.Length - 1]}"; // take first and last values for row
        string column = current_string.Substring(1, current_string.Length - 2); // take values from 1 to (end-1) for column
        int value = S_Boxes[i][Convert.ToInt32(row, 2), Convert.ToInt32(column, 2)]; // find value from current sBox [row column] 
        string subOutput = IntegerToBinary_4bits(value);
        FinalRes = FinalRes + subOutput;
    }
    return FinalRes;
}

```

### 7. 32-Bit Permutation

```csharp
static string permute(string input_binary)
{
    int[] Permutation_AfterSBOX = new int[32]{
            16,  7, 20, 21,
            29, 12, 28, 17,
            1, 15, 23, 26,
            5, 18, 31, 10,
            2,  8, 24, 14,
            32, 27,  3,  9,
            19, 13, 30,  6,
            22, 11,  4, 25
    };
    string output_binary = "";
    for (int i = 0; i < Permutation_AfterSBOX.Length; i++)
    {
        output_binary += input_binary[Permutation_AfterSBOX[i] - 1];
    }
    return output_binary;
}
```

### 8. Encryption

```csharp
public static string Encrypt(string plainText, string key)
{
    init_encode_map();
    plainText = plainText.ToUpper();
    key = key.ToUpper();

    key = key.Substring(2, key.Length - 2);
    plainText = plainText.Substring(2, plainText.Length - 2);
  
    key = HexWord_to_BinWord(key);
    plainText = HexWord_to_BinWord(plainText);

    string[] keyRounds_48bits = PC_2(keying(key));


    string ip_64bit = IP(plainText);
    string[] Left_Right = splitPlainTextTo2Strings(ip_64bit);
    string L_32bit = Left_Right[0];
    string R_32bit = Left_Right[1];
    for (int i = 0; i < 16; i++)
    {
        string TempR_32_bit = R_32bit;
        string R_afterExpansion_48bit = expandString(R_32bit);
        string R_afterXorKey_48bit = XOR(new List<string>(){ R_afterExpansion_48bit, keyRounds_48bits[i]});
        string R_afterSBox_32bit = sbox_48_to_32(R_afterXorKey_48bit);
        string R_afterPermute_32bit = permute(R_afterSBox_32bit);
        R_32bit = XOR(new List<string>() { R_afterPermute_32bit, L_32bit });
        L_32bit = TempR_32_bit;
    }
    string ipRev_64bit = IP(R_32bit + L_32bit, true);
  
    return "0x" + BinWord_to_HexWord(ipRev_64bit);
}

```

### 9. Decryption

```csharp
public static string Decrypt(string cipherText, string key)
{
    cipherText = cipherText.ToUpper();
    key = key.ToUpper();
    init_encode_map();

    key = key.Substring(2, key.Length - 2);
    cipherText = cipherText.Substring(2, cipherText.Length - 2);

    key = HexWord_to_BinWord(key);
    cipherText = HexWord_to_BinWord(cipherText);

    string[] keyRounds_48bits = PC_2(keying(key));

    string ip_64bit = IP(cipherText);
    string[] Left_Right = splitPlainTextTo2Strings(ip_64bit);
    string L_32bit = Left_Right[0];
    string R_32bit = Left_Right[1];


    for (int i = 15; i >= 0; i--)
    {
        string TempR_32_bit = R_32bit;
        string R_afterExpansion_48bit = expandString(R_32bit);
        string R_afterXorKey_48bit = XOR(new List<string>() { R_afterExpansion_48bit, keyRounds_48bits[i] });
        string R_afterSBox_32bit = sbox_48_to_32(R_afterXorKey_48bit);
        string R_afterPermute_32bit = permute(R_afterSBox_32bit);
        R_32bit = XOR(new List<string>() { R_afterPermute_32bit, L_32bit });
        L_32bit = TempR_32_bit;
    }
    string ipRev_64bit = IP(R_32bit + L_32bit, true);

    return "0x" + BinWord_to_HexWord(ipRev_64bit);

}
```
