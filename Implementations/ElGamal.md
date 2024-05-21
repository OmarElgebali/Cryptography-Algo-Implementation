# ElGamal Algorithm

### Test Cases

```csharp
List<long> cipher = Encrypt(7187, 4842, 4464, 19, 19);//191
Assert.AreEqual(cipher[0], 2781);
Assert.AreEqual(cipher[1], 437);

List<long> cipher = Encrypt(6323, 4736, 2231, 58, 111);//118
Assert.AreEqual(cipher[0], 6066);
Assert.AreEqual(cipher[1], 899);

int plain = algorithm.Decrypt(2781, 437, 191, 7187);
Assert.AreEqual(plain, 19);

int plain = algorithm.Decrypt(6066, 899, 118, 6323);
Assert.AreEqual(plain, 111);
```

### 1. Encrypt Algorithm

```csharp
public List<long> Encrypt(int q, int alpha, int y, int k, int m)
{
    return new List<long>(){largePower(alpha, k, q),(largePower(y, k, q) * m) % q};  
}
```

### 2. Decryption Algorithm

```csharp
public int Decrypt(int c1, int c2, int x, int q)
{
    return (c2 * ExtendedEuclid.GetMultiplicativeInverse(largePower(c1, x, q), q))%q;
}
```
