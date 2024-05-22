# Advanced Encryption Standard (AES)

### Test Cases

```csharp
string mainPlain = "0x3243F6A8885A308D313198A2e0370734";
string mainCipher = "0x3925841D02DC09FBDC118597196A0B32";
string mainKey = "0x2B7E151628AED2A6ABF7158809CF4F3C";

string mainPlain2 = "0x00000000000000000000000000000001";
string mainCipher2 = "0x58e2fccefa7e3061367f1d57a4e7455a";
string mainKey2 = "0x00000000000000000000000000000000";

string mainPlain3 = "0x00112233445566778899aabbccddeeff";
string mainCipher3 = "0x69c4e0d86a7b0430d8cdb78070b4c55a";
string mainKey3 = "0x000102030405060708090a0b0c0d0e0f";

string mainPlain4 = "0x54776F204F6E65204E696E652054776F";
string mainCipher4 = "0x29C3505F571420F6402299B31A02D73A";
string mainKey4 = "0x5468617473206D79204B756E67204675";
```

### 1. Encode Mapping (Hexa <-> Digit)

```csharp
static Dictionary<char, string> encode_mapping = new Dictionary<char, string>();

static string X_to_b(string hexaPart)
{
    return encode_mapping[hexaPart[0]] + encode_mapping[hexaPart[1]];
}

static string B_to_x(string binaryPart)
{
    return FindKeyByValue(binaryPart.Substring(0, 4)) + FindKeyByValue(binaryPart.Substring(4, 4));
}

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
```

### 2. Helpers
1. Convert matrix to string colwise
2. Convert string to matrix colwise
3. Convert string to matrix rowwise
4. Rotate column (N-Shifts)
5. XOR operation between list of binaries (a XOR b XOR c XOR ...) 
6. XOR operation between list of list of binaries ([,,] XOR [,,] XOR [,,] XOR ...) 
7. Print Matrix

```csharp
static string matrix_to_string_colwise(List<List<string>> mat)
{
    string output = "";
    for (int i = 0; i < 4; i++)
    {
        for (int j = 0; j < 4; j++)
        {
            output += mat[i][j];
        }
    }
    return output;
}

static List<List<string>> string_to_matrix_colwise(string str, bool remove_indications = true)
{
    if (remove_indications)
        str = str.Substring(2, str.Length - 2);
    List<List<string>> listlist = new List<List<string>>();
    for (int j = 0; j < str.Length; j += 8)
    {
        List<string> list = new List<string>();
        list.Add($"{str[j]}{str[j + 1]}");
        list.Add($"{str[j + 2]}{str[j + 3]}");
        list.Add($"{str[j + 4]}{str[j + 5]}");
        list.Add($"{str[j + 6]}{str[j + 7]}");
        listlist.Add(list);
    }
    return listlist;
}

static List<List<string>> string_to_matrix_rowwise(string str, bool remove_indications = true)
{
    if (remove_indications)
        str = str.Substring(2, str.Length - 2);
    List<List<string>> listlist = new List<List<string>>();
    for (int i = 0; i < 4; i++)
        listlist.Add(new List<string>());

    for (int i = 0; i < str.Length; i += 2)
    {
        listlist[(i / 2) / 4].Add($"{str[i]}{str[i + 1]}");
    }
    return listlist;
}

static List<string> RotateCol(List<string> col, int n_rotations)
{
    List<string> rotated_col = new List<string>();
    rotated_col.AddRange(col);
    for (int i = 0; i < n_rotations; i++)
    {
        rotated_col.RemoveAt(0);
        rotated_col.Add(col[i]);
    }
    return rotated_col;
}

static List<string> XOR_List(List<List<string>> hexasList, bool encode = true)
{
    List<string> xor_result = new List<string>();
    for (int i = 0; i < 4; i++)
    {
        List<string> to_be_XORed = new List<string>();
        foreach (var list in hexasList)
            to_be_XORed.Add(list[i]);
        xor_result.Add(XOR(to_be_XORed));
    }
    return xor_result;
}

static string XOR(List<string> hexas, bool encode = true)
{
    string output = "";
    List<string> binary = new List<string>();
    for (int i = 0; i < hexas.Count; i++)
    {
        binary.Add(X_to_b(hexas[i]));
    }
    for (int i = 0; i < 8; i++)
    {
        int col_sum = 0;
        for (int j = 0; j < hexas.Count; j++)
        {
            col_sum += (binary[j][i] == '1') ? 1 : 0;
        }
        output += (col_sum % 2 != 0) ? '1' : '0';
    }
    if (!encode)
        return output;
    return B_to_x(output);
}

static void print_matrix(List<List<string>> listlist, string title)
{
    Console.WriteLine($"{title}:");
    for (int i = 0; i < 4; i++)
    {
        for (int j = 0; j < 4; j++)
        {
            Console.Write($"{listlist[j][i]} ");
        }
        Console.WriteLine();
    }
}
```

### 3. Key Generation

```csharp
static List<List<List<string>>> key_rounds = new List<List<List<string>>>();
static List<List<string>> Rcon = new List<List<string>>()
{
    new List<string> { "01", "00", "00", "00" },
    new List<string> { "02", "00", "00", "00" },
    new List<string> { "04", "00", "00", "00" },
    new List<string> { "08", "00", "00", "00" },
    new List<string> { "10", "00", "00", "00" },
    new List<string> { "20", "00", "00", "00" },
    new List<string> { "40", "00", "00", "00" },
    new List<string> { "80", "00", "00", "00" },
    new List<string> { "1B", "00", "00", "00" },
    new List<string> { "36", "00", "00", "00" }
};

static void KeySchedule(string key, bool is_Dec = false)
{
    key_rounds.Clear();
    key_rounds = new List<List<List<string>>>();
    List<List<string>> initial_key = string_to_matrix_rowwise(key, remove_indications: true);
    key_rounds.Add(initial_key);
    for (int round = 0; round < 10; round++)
    {
        List<List<string>> curr_key = new List<List<string>>();
        for (int j = 0; j < 4; j++)
            curr_key.Add(new List<string>());

        /* Column 1 */
        curr_key[0] = RotateCol(key_rounds.Last()[3], 1);
        curr_key[0] = SubBytes_List(curr_key[0]);
        List<List<string>> to_be_XORed = new List<List<string>>
        {
            key_rounds.Last()[0],
            curr_key[0],
            Rcon[round]
        };
        curr_key[0] = XOR_List(to_be_XORed);

        /* Columns 2,3,4 */
        for (int col = 1; col < 4; col++)
        {
            to_be_XORed = new List<List<string>>
            {
                key_rounds.Last()[col],
                curr_key[col - 1]
            };
            curr_key[col] = XOR_List(to_be_XORed);
        }

        key_rounds.Add(curr_key);
    }
    if (is_Dec)
        key_rounds.Reverse();
}
```

### 4. Add Round Key

```csharp
static List<List<string>> AddRoundKey(List<List<string>> state, List<List<string>> key)
{
    List<List<string>> result = new List<List<string>>();
    for (int i = 0; i < state.Count; i++)
    {
        List<List<string>> mul = new List<List<string>>();
        mul.Add(state[i]);
        mul.Add((List<string>)key[i]);
        result.Add(XOR_List(mul));
    }
    return result;

}
```

### 5. Shift Rows & Its Inverse

```csharp
static List<List<string>> ShiftRow(List<List<string>> Matrix)
{
    string temp = null;
    for (int j = 0; j < 4; j++)// rows 
    {
        for (int i = 0; i < j; i++)
        {
            temp = Matrix[0][j];
            for (int k = 1; k < 4; k++)
            {
                Matrix[k - 1][j] = Matrix[k][j];
            }
            Matrix[3][j] = temp;
        }
    }
    return Matrix;
}

static List<List<string>> InvShiftRow(List<List<string>> Matrix)
{
    string temp = null;
    for (int j = 0; j < 4; j++)// rows 
    {
        for (int i = 0; i < j; i++)
        {
            temp = Matrix[3][j];
            for (int k = 3; k > 0; k--)
            {
                Matrix[k][j] = Matrix[k - 1][j];
            }
            Matrix[0][j] = temp;
        }
    }
    return Matrix;
}
```

### 6. Sub Bytes
```csharp
static string[,] sbox;

static int HexIndexing(char hexa)
{
    /*
        * 0: 48 -> index = 0
        * ...
        * 9: 57 -> index = 9
        * A: 65 -> index = 10
        * ...
        * F: 70 -> index = 15
        */
    return ((int)hexa > 57) ? ((int)hexa - 65 + 10) : ((int)hexa - 48);
}

static List<List<string>> SubBytesMatrix(List<List<string>> Matrix)
{
    int len = Matrix.Count;
    for (int i = 0; i < len; i++)
    {
        Matrix[i] = SubBytes_List(Matrix[i]);
    }
    return Matrix;
}

static List<string> SubBytes_List(List<string> hexas)
{
    return hexas.Select(hexa => SubBytes(hexa)).ToList();
}

static string SubBytes(string input)
{
    char row = input[0];
    char col = input[1];
    int row_index = HexIndexing(row);
    int col_index = HexIndexing(col);
    return sbox[row_index, col_index];
}
```

### 7. Mix Column & Its Inverse

```csharp
static string multiply_02(string inputHex)
{
    string Bin = X_to_b(inputHex);
    char first = Bin[0];
    Bin = Bin.Substring(1) + '0';
    if (first == '1')
    {
        Bin = XOR(new List<string> {
            B_to_x(Bin),
            B_to_x("00011011")
        }, encode:false);
    }
    Bin = B_to_x(Bin);
    return Bin;

}
static string multiply_03(string inputHex)
{
    string Bin02 = multiply_02(inputHex);
    string Bin03 = XOR(new List<string> {
        Bin02,
        inputHex
    });
    Console.WriteLine($"03 ({Bin03}): {X_to_b(Bin03)}");
    return Bin03;

}
static string multiply_0E(string inputHex)
{
    string firstShift = multiply_02(inputHex);
    string secondShift = multiply_02(firstShift);
    string thirdShift = multiply_02(secondShift);

    string Bin0E = XOR(new List<string> {
        firstShift,
        secondShift,
        thirdShift
    });
    return Bin0E;
    }
static string multiply_0D(string inputHex)
{
    string firstShift = multiply_02(inputHex);
    string secondShift = multiply_02(firstShift);
    string thirdShift = multiply_02(secondShift);

    string Bin0D = XOR(new List<string> {
        secondShift,
        thirdShift,
        inputHex
    });
    return Bin0D;
}
static string multiply_0B(string inputHex)
{
    string firstShift = multiply_02(inputHex);
    string secondShift = multiply_02(firstShift);
    string thirdShift = multiply_02(secondShift);

    string Bin0B = XOR(new List<string> {
        firstShift,
        thirdShift,
        inputHex
    });
    return Bin0B;
}
static string multiply_09(string inputHex)
{
    string firstShift = multiply_02(inputHex);
    string secondShift = multiply_02(firstShift);
    string thirdShift = multiply_02(secondShift);

    string Bin09 = XOR(new List<string> {
        thirdShift,
        inputHex
    });
    return Bin09;
}
static string MixCol_Element(List<string> matrix)
{
    /*
        02
        03
        01
        01
        */
    return XOR(new List<string>
    {
        multiply_02(matrix[0]),
        multiply_03(matrix[1]),
        matrix[2],
        matrix[3]
    });
}

static List<List<string>> MixCol(List<List<string>> matrix)
{
    List<List<string>> res = new List<List<string>>();
    for (int i = 0; i < 4; i++)
    {
        res.Add(new List<string>
        {
            MixCol_Element(matrix[i]),
            MixCol_Element(RotateCol(matrix[i], 1)),
            MixCol_Element(RotateCol(matrix[i], 2)),
            MixCol_Element(RotateCol(matrix[i], 3)),
        });
    }
    return res;
}
static List<List<string>> InverseMixCol(List<List<string>> matrix)
{
    List<List<string>> res = new List<List<string>>()
    {
        new List<string> {},
        new List<string> {},
        new List<string> {},
        new List<string> {},
    };
    List<string> result = new List<string>();
    List<List<string>> constMat = new List<List<string>>()
    {
        new List<string> { "0E", "09", "0D", "0B" },
        new List<string> { "0B", "0E", "09", "0D" },
        new List<string> { "0D", "0B", "0E", "09" },
        new List<string> { "09", "0D", "0B", "0E" },
    };
    for (int k = 0; k < 4; k++)
    {
        for (int i = 0; i < 4; i++)
        {
            for (int j = 0; j < 4; j++)
            {
                string data = constMat[j][i];
                switch (data)
                {
                    case "0E":
                        result.Add(multiply_0E(matrix[k][j]));
                        break;
                    case "0D":
                        result.Add(multiply_0D(matrix[k][j]));
                        break;
                    case "0B":
                        result.Add(multiply_0B(matrix[k][j]));
                        break;
                    case "09":
                        result.Add(multiply_09(matrix[k][j]));
                        break;
                }
            }
            string xorCell = XOR(result);
            res[k].Add(xorCell);
            result.Clear();
        }
    }
    return res;
}
```

### 8. Encryption

```csharp
public static string Encrypt(string plainText, string key)
{
    init_encode_map();
    key = key.ToUpper();
    plainText = plainText.ToUpper();
    sbox = new string[16, 16]
    {
        {"63", "7C", "77", "7B", "F2", "6B", "6F", "C5", "30", "01", "67", "2B", "FE", "D7", "AB", "76"},
        {"CA", "82", "C9", "7D", "FA", "59", "47", "F0", "AD", "D4", "A2", "AF", "9C", "A4", "72", "C0"},
        {"B7", "FD", "93", "26", "36", "3F", "F7", "CC", "34", "A5", "E5", "F1", "71", "D8", "31", "15"},
        {"04", "C7", "23", "C3", "18", "96", "05", "9A", "07", "12", "80", "E2", "EB", "27", "B2", "75"},
        {"09", "83", "2C", "1A", "1B", "6E", "5A", "A0", "52", "3B", "D6", "B3", "29", "E3", "2F", "84"},
        {"53", "D1", "00", "ED", "20", "FC", "B1", "5B", "6A", "CB", "BE", "39", "4A", "4C", "58", "CF"},
        {"D0", "EF", "AA", "FB", "43", "4D", "33", "85", "45", "F9", "02", "7F", "50", "3C", "9F", "A8"},
        {"51", "A3", "40", "8F", "92", "9D", "38", "F5", "BC", "B6", "DA", "21", "10", "FF", "F3", "D2"},
        {"CD", "0C", "13", "EC", "5F", "97", "44", "17", "C4", "A7", "7E", "3D", "64", "5D", "19", "73"},
        {"60", "81", "4F", "DC", "22", "2A", "90", "88", "46", "EE", "B8", "14", "DE", "5E", "0B", "DB"},
        {"E0", "32", "3A", "0A", "49", "06", "24", "5C", "C2", "D3", "AC", "62", "91", "95", "E4", "79"},
        {"E7", "C8", "37", "6D", "8D", "D5", "4E", "A9", "6C", "56", "F4", "EA", "65", "7A", "AE", "08"},
        {"BA", "78", "25", "2E", "1C", "A6", "B4", "C6", "E8", "DD", "74", "1F", "4B", "BD", "8B", "8A"},
        {"70", "3E", "B5", "66", "48", "03", "F6", "0E", "61", "35", "57", "B9", "86", "C1", "1D", "9E"},
        {"E1", "F8", "98", "11", "69", "D9", "8E", "94", "9B", "1E", "87", "E9", "CE", "55", "28", "DF"},
        {"8C", "A1", "89", "0D", "BF", "E6", "42", "68", "41", "99", "2D", "0F", "B0", "54", "BB", "16"}
    };
    KeySchedule(key);
    List<List<string>> input = string_to_matrix_colwise(plainText);
    List<List<string>> OriginalMat_AfterAddRoundKey = AddRoundKey(input, key_rounds[0]);
    List<List<string>> OriginalMat_AfterSubBytes = new List<List<string>>();
    List<List<string>> OriginalMat_AfterShift = new List<List<string>>();
    List<List<string>> OriginalMat_AfterMixCol = new List<List<string>>();
    for (int i = 1; i <= 9; i++)
    {
        OriginalMat_AfterSubBytes = SubBytesMatrix(OriginalMat_AfterAddRoundKey);
        OriginalMat_AfterShift = ShiftRow(OriginalMat_AfterSubBytes);
        OriginalMat_AfterMixCol = MixCol(OriginalMat_AfterShift);
        OriginalMat_AfterAddRoundKey = AddRoundKey(OriginalMat_AfterMixCol, key_rounds[i]);
    }
    OriginalMat_AfterSubBytes = SubBytesMatrix(OriginalMat_AfterAddRoundKey);
    OriginalMat_AfterShift = ShiftRow(OriginalMat_AfterSubBytes);
    OriginalMat_AfterAddRoundKey = AddRoundKey(OriginalMat_AfterShift, key_rounds[10]);
    return "0x" + matrix_to_string_colwise(OriginalMat_AfterAddRoundKey);
}
```

### 9. Decryption

```csharp
public static string Decrypt(string cipherText, string key)
{
    cipherText = cipherText.ToUpper();
    key = key.ToUpper();
    init_encode_map();
    sbox = new string[16, 16]
    {
        {"63", "7C", "77", "7B", "F2", "6B", "6F", "C5", "30", "01", "67", "2B", "FE", "D7", "AB", "76"},
        {"CA", "82", "C9", "7D", "FA", "59", "47", "F0", "AD", "D4", "A2", "AF", "9C", "A4", "72", "C0"},
        {"B7", "FD", "93", "26", "36", "3F", "F7", "CC", "34", "A5", "E5", "F1", "71", "D8", "31", "15"},
        {"04", "C7", "23", "C3", "18", "96", "05", "9A", "07", "12", "80", "E2", "EB", "27", "B2", "75"},
        {"09", "83", "2C", "1A", "1B", "6E", "5A", "A0", "52", "3B", "D6", "B3", "29", "E3", "2F", "84"},
        {"53", "D1", "00", "ED", "20", "FC", "B1", "5B", "6A", "CB", "BE", "39", "4A", "4C", "58", "CF"},
        {"D0", "EF", "AA", "FB", "43", "4D", "33", "85", "45", "F9", "02", "7F", "50", "3C", "9F", "A8"},
        {"51", "A3", "40", "8F", "92", "9D", "38", "F5", "BC", "B6", "DA", "21", "10", "FF", "F3", "D2"},
        {"CD", "0C", "13", "EC", "5F", "97", "44", "17", "C4", "A7", "7E", "3D", "64", "5D", "19", "73"},
        {"60", "81", "4F", "DC", "22", "2A", "90", "88", "46", "EE", "B8", "14", "DE", "5E", "0B", "DB"},
        {"E0", "32", "3A", "0A", "49", "06", "24", "5C", "C2", "D3", "AC", "62", "91", "95", "E4", "79"},
        {"E7", "C8", "37", "6D", "8D", "D5", "4E", "A9", "6C", "56", "F4", "EA", "65", "7A", "AE", "08"},
        {"BA", "78", "25", "2E", "1C", "A6", "B4", "C6", "E8", "DD", "74", "1F", "4B", "BD", "8B", "8A"},
        {"70", "3E", "B5", "66", "48", "03", "F6", "0E", "61", "35", "57", "B9", "86", "C1", "1D", "9E"},
        {"E1", "F8", "98", "11", "69", "D9", "8E", "94", "9B", "1E", "87", "E9", "CE", "55", "28", "DF"},
        {"8C", "A1", "89", "0D", "BF", "E6", "42", "68", "41", "99", "2D", "0F", "B0", "54", "BB", "16"}
    };
    KeySchedule(key, is_Dec: true);
    sbox = new string[16, 16]
    {
        {"52", "09", "6A", "D5", "30", "36", "A5", "38", "BF", "40", "A3", "9E", "81", "F3", "D7", "FB"},
        {"7C", "E3", "39", "82", "9B", "2F", "FF", "87", "34", "8E", "43", "44", "C4", "DE", "E9", "CB"},
        {"54", "7B", "94", "32", "A6", "C2", "23", "3D", "EE", "4C", "95", "0B", "42", "FA", "C3", "4E"},
        {"08", "2E", "A1", "66", "28", "D9", "24", "B2", "76", "5B", "A2", "49", "6D", "8B", "D1", "25"},
        {"72", "F8", "F6", "64", "86", "68", "98", "16", "D4", "A4", "5C", "CC", "5D", "65", "B6", "92"},
        {"6C", "70", "48", "50", "FD", "ED", "B9", "DA", "5E", "15", "46", "57", "A7", "8D", "9D", "84"},
        {"90", "D8", "AB", "00", "8C", "BC", "D3", "0A", "F7", "E4", "58", "05", "B8", "B3", "45", "06"},
        {"D0", "2C", "1E", "8F", "CA", "3F", "0F", "02", "C1", "AF", "BD", "03", "01", "13", "8A", "6B"},
        {"3A", "91", "11", "41", "4F", "67", "DC", "EA", "97", "F2", "CF", "CE", "F0", "B4", "E6", "73"},
        {"96", "AC", "74", "22", "E7", "AD", "35", "85", "E2", "F9", "37", "E8", "1C", "75", "DF", "6E"},
        {"47", "F1", "1A", "71", "1D", "29", "C5", "89", "6F", "B7", "62", "0E", "AA", "18", "BE", "1B"},
        {"FC", "56", "3E", "4B", "C6", "D2", "79", "20", "9A", "DB", "C0", "FE", "78", "CD", "5A", "F4"},
        {"1F", "DD", "A8", "33", "88", "07", "C7", "31", "B1", "12", "10", "59", "27", "80", "EC", "5F"},
        {"60", "51", "7F", "A9", "19", "B5", "4A", "0D", "2D", "E5", "7A", "9F", "93", "C9", "9C", "EF"},
        {"A0", "E0", "3B", "4D", "AE", "2A", "F5", "B0", "C8", "EB", "BB", "3C", "83", "53", "99", "61"},
        {"17", "2B", "04", "7E", "BA", "77", "D6", "26", "E1", "69", "14", "63", "55", "21", "0C", "7D"}
    };
    List<List<string>> input = string_to_matrix_colwise(cipherText);
    List<List<string>> addRoundKey = AddRoundKey(input, key_rounds[0]);
    List<List<string>> InversedShiftRow = new List<List<string>>();
    List<List<string>> InversedSubByte = new List<List<string>>();
    List<List<string>> inverseMixCol = new List<List<string>>();

    InversedShiftRow = InvShiftRow(addRoundKey);
    InversedSubByte = SubBytesMatrix(InversedShiftRow);
    for (int i = 1; i <= 9; i++)
    {
        addRoundKey = AddRoundKey(InversedSubByte, key_rounds[i]);
        inverseMixCol = InverseMixCol(addRoundKey);
        InversedShiftRow = InvShiftRow(inverseMixCol);
        InversedSubByte = SubBytesMatrix(InversedShiftRow);
    }
    addRoundKey = AddRoundKey(InversedSubByte, key_rounds[10]);
    return "0x" + matrix_to_string_colwise(addRoundKey);
}
```