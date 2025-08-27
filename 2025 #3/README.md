# 2025 Cryptanalysis Contest #3 Solution

이하의 notation은 wbcaes128.c 구현 과정에서 참고된 [Muir의 whitebox AES tutorial](https://eprint.iacr.org/2013/104.pdf)을 따른다.

문제에서 주어진 구현체를 살펴보면 모든 Tybox가 0x00000000을 포함한다는 점을 확인할 수 있다.

이로부터 본 구현체에는 internal encoding이 전혀 적용되지 않았거나 적용되었더라도 항상 0을 0으로 보낸다, 즉 다음이 성립할 것으로 추측하였다:

$$
g(Ty(T_i^1(x))) = 0 \Leftrightarrow Ty(T_i^1(x)) = 0 \Leftrightarrow T_i^1(x) = 0 \Leftrightarrow x = \hat{k}_0[i] \oplus 82
$$

이는 구현 과정에 random encoding이 적용되었을 경우 이러한 결과가 나올 확률은 매우 희박하기 때문으로, 실제로도 [문제에서 주어진 구현체를 생성하는 코드](https://github.com/balena/aes-whitebox/blob/master/aes_whitebox_compiler.cc)를 살펴보면 internal encoding인 $MB$와 $L$이 모두 invertible matrix여서 본 구현체의 internal encoding은 항상 0을 0으로 보내게 된다.

![MB_L_implementation.png](https://github.com/user-attachments/assets/2ba3cca8-4211-4d6e-97df-60e966ba043b)

즉  $Ty$ table의 정의상 $Ty(S(82)) = Ty(0) = 0$이 성립함을 고려하면 수식의 오른쪽 두 등식과 같은 논리로 $\hat{k}_0$를 복구할 수 있으며, 그 값은 43 43 73 35 74 65 32 70 74 30 79 6e 32 72 6f 74이다.

이때 원본 AES와 whitebox AES는 ShiftRows가 적용되는 시점이 서로 다르므로, 복구한 값에 ShiftRows를 적용한 후 ASCII로 변환하면 마스터키 **CryptContest2025** 를 얻는다.
