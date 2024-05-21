# Power Function 
Used to overcome overflow using the mod.

### Implementation
```csharp
public static int largePower(int baseNumber,int power,int mod)
{
     int result = 1;
     for (int i = 0; i < power; i++)
     {
         result *= baseNumber;
         result = (result % mod + mod) % mod;
     }
     return (result % mod + mod) % mod;
}
```