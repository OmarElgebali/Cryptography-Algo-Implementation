# Diffie Hillman Algorithm

### Test Cases

```csharp
List<int> key = GetKeys(19, 2, 6, 13);
Assert.AreEqual(key[0], 7);
Assert.AreEqual(key[1], 7);

List<int> key = GetKeys(353, 2, 97, 233);
Assert.AreEqual(key[0], 81);
Assert.AreEqual(key[1], 81);

List<int> key = GetKeys(353, 3, 97, 233);
Assert.AreEqual(key[0], 160);
Assert.AreEqual(key[1], 160);

List<int> key = GetKeys(541, 10, 50, 100);
Assert.AreEqual(key[0], 449);
Assert.AreEqual(key[1], 449);
```

### Get Keys

```csharp
public List<int> GetKeys(int q, int alpha, int xa, int xb)
{
    int Ya = power(alpha, xa, q), Yb = power(alpha, xb, q);
    int KeyA = power(Yb, xa, q), KeyB = power(Ya, xb, q);
    List<int> Keys = new List<int>();
    Keys.Add(KeyA);
    Keys.Add(KeyB);
    return Keys;
}
```