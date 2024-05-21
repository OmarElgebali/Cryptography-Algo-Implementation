# RSA (public-key encryption) Technique

### Test Cases

```csharp
int cipher = Encrypt(11, 17, 88, 7);
Assert.AreEqual(cipher, 11);

int plain = Decrypt(11, 17, 11, 7);
Assert.AreEqual(plain, 88);

int cipher = Encrypt(13, 19, 65, 5);
Assert.AreEqual(cipher, 221);

int plain = Decrypt(13, 19, 221, 5);
Assert.AreEqual(plain, 65);

int cipher = Encrypt(61, 53, 70, 7);
Assert.AreEqual(cipher, 2338);

int plain = Decrypt(61, 53, 2338, 7);
Assert.AreEqual(plain, 70);

int cipher = Encrypt(257, 337, 18537, 17);
Assert.AreEqual(cipher, 12448);

int plain = Decrypt(257, 337, 12448, 17);
Assert.AreEqual(plain, 18537);
```

### 1. Encrypt Algorithm

```csharp
public int Encrypt(int p, int q, int M, int e)
{
    Console.WriteLine($"p: {p}, q: {q}, M: {M}, e: {e}");
    Console.WriteLine();
    int n = p*q;

    int C = power(M, e, n);
    Console.WriteLine($"C: {C}");

    return C;

}
```

### 2. Decryption Algorithm

```csharp
public int Decrypt(int p, int q, int C, int e)
{
    Console.WriteLine($"p: {p}, q: {q}, M: {C}, e: {e}");
    Console.WriteLine();
    int n = p * q;
    int totient = (p - 1) * (q - 1);

    int d = ExtendedEuclid.GetMultiplicativeInverse(e, totient);
    Console.WriteLine($"D: {d}");

    int M = power(C, d, n);
    Console.WriteLine($"C: {M}");

    return M;
}
```
