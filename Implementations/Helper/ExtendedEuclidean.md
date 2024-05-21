# Extended Euclidean

### Test Cases

```csharp
// Test 1
int res = ExtendedEuclid.GetMultiplicativeInverse(23, 26);
Assert.AreEqual(res, 17);

// Test 2
int res = ExtendedEuclid.GetMultiplicativeInverse(22, 26);
Assert.AreEqual(res, -1);

// Test 3
int res = ExtendedEuclid.GetMultiplicativeInverse(50, 71);
Assert.AreEqual(res, 27);

// Test 4
int res = ExtendedEuclid.GetMultiplicativeInverse(43, 64);
Assert.AreEqual(res, 3);

// Test 5
int res = ExtendedEuclid.GetMultiplicativeInverse(1111, 22222);
Assert.AreEqual(res, 11101);

// Test 6
int res = ExtendedEuclid.GetMultiplicativeInverse(123456789, 1236);
Assert.AreEqual(res, -1);

// Test 7
int res = ExtendedEuclid.GetMultiplicativeInverse(123456789, 12365);
Assert.AreEqual(res, 3729);

// Test 8
int res = ExtendedEuclid.GetMultiplicativeInverse(13245687, 135469);
Assert.AreEqual(res, 38164);
```

### Algorithm

```csharp
public class ExtendedEuclid
{
    /// <summary>
    /// 
    /// </summary>
    /// <param name="number"></param>
    /// <param name="baseN"></param>
    /// <returns>Mul inverse, -1 if no inv</returns>
    public static int GetMultiplicativeInverse(int number, int baseN)
    {
        int Q;
        int[] A = { 1, 0, baseN };
        int[] B = { 0, 1, number };
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
        if (B[2] != 1)
            return -1;
        if (B[1] < 0)
            return (B[1] % baseN + baseN) % baseN;
        return B[1];
    }

}```