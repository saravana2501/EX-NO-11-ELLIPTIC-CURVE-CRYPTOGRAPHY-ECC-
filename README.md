# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
#include <stdio.h>
#include <stdlib.h>

typedef struct
{
    int x;
    int y;
    int is_infinity;
} Point;

const int a = 2;
const int b = 3;
const int p = 17;

int mod(int value)
{
    int result = value % p;
    return (result < 0) ? result + p : result;
}

int mod_inverse(int k)
{
    k = mod(k);
    for (int x = 1; x < p; x++)
        if ((k * x) % p == 1)
            return x;
    return -1;
}

Point point_addition(Point P, Point Q)
{
    Point result;
    if (P.is_infinity) return Q;
    if (Q.is_infinity) return P;

    if (P.x == Q.x && (P.y + Q.y) % p == 0)
    {
        result.is_infinity = 1;
        return result;
    }

    int lambda;
    if (P.x == Q.x && P.y == Q.y)
    {
        int numerator = mod(3 * P.x * P.x + a);
        int denominator = mod_inverse(2 * P.y);
        if (denominator == -1)
        {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod(numerator * denominator);
    }
    else
    {
        int numerator = mod(Q.y - P.y);
        int denominator = mod_inverse(Q.x - P.x);
        if (denominator == -1)
        {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod(numerator * denominator);
    }

    result.x = mod(lambda * lambda - P.x - Q.x);
    result.y = mod(lambda * (P.x - result.x) - P.y);
    result.is_infinity = 0;
    return result;
}

Point scalar_multiplication(Point P, int n)
{
    Point result;
    result.is_infinity = 1;
    Point addend = P;

    while (n > 0)
    {
        if (n & 1)
            result = point_addition(result, addend);
        addend = point_addition(addend, addend);
        n >>= 1;
    }

    return result;
}

int main()
{
    Point G = {5, 1, 0};
    int n = 7;

    printf("Elliptic Curve: y^2 = x^3 + %dx + %d over F_%d\n", a, b, p);
    printf("Base point G: (%d, %d)\n", G.x, G.y);

    Point R = scalar_multiplication(G, n);

    if (R.is_infinity)
        printf("Result of %d * G: Point at Infinity\n", n);
    else
        printf("Result of %d * G: (%d, %d)\n", n, R.x, R.y);

    return 0;
}

```


## Output:

<img width="1008" height="527" alt="Screenshot 2025-10-29 082610" src="https://github.com/user-attachments/assets/670f0336-ab30-4f7b-b32d-959f795ad693" />


## Result:

The ECC program successfully demonstrates point addition and scalar multiplication on an elliptic 
curve over a finite field, and the simulation has been executed and verified successfully.
