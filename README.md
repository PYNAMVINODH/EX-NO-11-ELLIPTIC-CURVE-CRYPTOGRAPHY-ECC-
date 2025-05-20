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
```c
#include <stdio.h>

#define A 2
#define B 2
#define FIELD_P 17

typedef struct {
    int x, y;
    int is_infinity;  // 1 if point at infinity, else 0
} Point;

int mod(int a, int p) {
    a %= p;
    return (a < 0) ? a + p : a;
}

int mod_inverse(int a, int p) {
    a = mod(a, p);
    for (int x = 1; x < p; x++) {
        if ((a * x) % p == 1)
            return x;
    }
    return -1; // no inverse found (shouldn't happen if p prime)
}

int is_on_curve(Point P) {
    if (P.is_infinity) return 1;
    int left = mod(P.y * P.y, FIELD_P);
    int right = mod(P.x * P.x * P.x + A * P.x + B, FIELD_P);
    return left == right;
}

Point point_add(Point P1, Point P2) {
    if (P1.is_infinity) return P2;
    if (P2.is_infinity) return P1;

    // If P1 == -P2, return point at infinity
    if (P1.x == P2.x && P1.y != P2.y) {
        return (Point){0, 0, 1};
    }

    int lambda;

    if (P1.x == P2.x && P1.y == P2.y) {
        // point doubling
        int numerator = mod(3 * P1.x * P1.x + A, FIELD_P);
        int denominator = mod(2 * P1.y, FIELD_P);
        int inv = mod_inverse(denominator, FIELD_P);
        lambda = mod(numerator * inv, FIELD_P);
    } else {
        // point addition
        int numerator = mod(P2.y - P1.y, FIELD_P);
        int denominator = mod(P2.x - P1.x, FIELD_P);
        int inv = mod_inverse(denominator, FIELD_P);
        lambda = mod(numerator * inv, FIELD_P);
    }

    int x3 = mod(lambda * lambda - P1.x - P2.x, FIELD_P);
    int y3 = mod(lambda * (P1.x - x3) - P1.y, FIELD_P);

    return (Point){x3, y3, 0};
}

Point scalar_mul(Point P, int k) {
    Point result = {0, 0, 1};  // point at infinity
    Point addend = P;

    while (k > 0) {
        if (k & 1) {
            result = point_add(result, addend);
        }
        addend = point_add(addend, addend);
        k >>= 1;
    }
    return result;
}

int main() {
    Point G = {5, 1, 0};
    int n = 7;

    if (!is_on_curve(G)) {
        printf("Base point G is NOT on the curve!\n");
        return 1;
    }

    printf("Base point G: (%d, %d)\n", G.x, G.y);
    Point R = scalar_mul(G, n);

    if (R.is_infinity) {
        printf("Result of %d * G is point at infinity\n", n);
    } else {
        printf("Result of %d * G: (%d, %d)\n", n, R.x, R.y);
        if (!is_on_curve(R)) {
            printf("Warning: result point NOT on curve!\n");
        }
    }
    return 0;
}

```

## Output:
![image](https://github.com/user-attachments/assets/1941ee5e-945d-4dfc-a3ec-28566dcad1df)


## Result:
The program is executed successfully

