# 2025 Crypto Analysis Contest #4 Solution

이하의 해독 과정 및 notation은 [D. Lee et al. (2021)](https://ieeexplore.ieee.org/document/9666909)을 따른다.

### H 구하기

<img width="680" height="151" alt="findH" src="https://github.com/user-attachments/assets/ea559f10-448c-4fff-9425-763385b20bdd" />

$H^t = \left[ h_1 | \cdots | h_{48} \right]$에 대해 $Gh_1 = \cdots = Gh_{48} = 0$이 성립하므로 $G$의 null space를 구함으로써 $H$를 구할 수 있다.

### M 구하기

<img width="643" height="189" alt="init" src="https://github.com/user-attachments/assets/e992098d-5abd-48d7-a760-ac50bdd4dacd" />


먼저 initialization mapping $R: \alpha \mapsto (\beta_1, \cdots, \beta_{18}, \beta_{20}, \cdots, \beta_{40}, \beta_{42}, \cdots, \beta_{63}, \beta_{65}, \cdots, \beta_{80})$을 살펴보면 XOR 연산의 특성상 $R(\alpha \oplus \Delta \alpha) = R(\alpha) \oplus R(\Delta \alpha)$가 성립한다. 문제에서는 frame number가 9867~9881로 주어졌으므로, frame number 변화에 따른 $R(\Delta \alpha)$를 미리 계산해두면 $N_0$ frame에서의 레지스터 초깃값에서 $N_i$ frame에서의 레지스터 초깃값을 바로 유도할 수 있다.

<img width="680" height="246" alt="findM" src="https://github.com/user-attachments/assets/b66a91ce-c9a1-4f5a-b25a-4ce8795aa66a" />

$M^{N_i}$는 $N_i$ frame에서의 $R_4$ 초깃값에 따라 결정되어 $x^{N_0}$를 $z^{N_i}$로 보내는 행렬이므로, 서로 선형독립인 656개 $x^{N_0}$에 대응되는 $z^{N_i}$를 구하면 $M^{N_i} = \left[ z_1^{N_i} | \cdots | z_{656}^{N_i} \right] \left[ x_1^{N_0} | \cdots | x_{656}^{N_0} \right] ^{-1}$를 계산할 수 있다. 문제에서 주어진 암호문은 error bit 2개를 포함하고 있으므로 적어도 13개의 오류가 없는 frame $\left( N_1^{\ast}, \cdots, N_{13}^{\ast} \right)$이 존재하며, 이에 대응되는 $HM^{N_i^{\ast}}$ 및 $r^{N_i^{\ast}}$들을 위와 같이 concatenate하면 $M$ 및 $r^{\ast}$를 얻는다.

### 방정식 풀기

<img width="680" height="302" alt="solveeq" src="https://github.com/user-attachments/assets/3d2a8a08-54df-493b-b9c9-ec6fd2aa0935" />

이제 가능한 $2^{16}$가지 $N_0$ frame에서의 $R_4$ 초깃값 및 $\binom{15}{13}$가지 $\left( N_1^{\ast}, \cdots, N_{13}^{\ast} \right)$ 조합을 전수조사하며 각 경우에 대응되는 방정식 $Mx = r^{\ast}$를 푼다. 만약 선택한 경우가 정답이 아니라면 augmented matrix를 Gaussian elimination했을 때 그림의 lower part 부분에 augment된 $M_{zero} \times r^{\ast}$ 부분이 영벡터가 아닐 것이므로, 해당 부분에서 1이 등장하는 경우는 버리는 식으로 코드를 설계하여 다음과 같이 각 레지스터 초깃값을 복구하였다:

<table>
  <tr>
    <td>
      $R_4$
    </td>
    <td>
      23053 (bit array $(\beta_{80}, \cdots, \beta_{65})$를 decimal로 변환한 값)
    </td>
  </tr>
  <tr>
    <td>
      $\left( N_1^{\ast}, \cdots, N_{13}^{\ast} \right)$
    </td>
    <td>
      $\left( 0, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14 \right)$ (frame number 9867을 0으로 인덱싱한 값)
    </td>
  </tr>
  <tr>
    <td>
      $(\beta_{18}, \cdots, \beta_{1}, \beta_{40}, \cdots, \beta_{20}, \beta_{63}, \cdots, \beta_{42})$
    </td>
    <td>
      $[1, 1, 1, 0, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1, 0, 1, 0, 0, 1, 0, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 1, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0]$
    </td>
  </tr>

### $K$ 및 평문 복구하기

논문에서 각 레지스터의 초깃값으로부터 keystream을 생성하는 과정을 상수항 \~ 이차항에 대한 연립방정식으로 표현한 것과 같은 원리로, $N_0$가 9867로 주어진 상황에서 $K$로부터 각 레지스터의 초깃값을 생성하는 과정은 상수항 \~ 일차항에 대한 연립방정식으로 표현할 수 있다. 따라서 $1 || K$를 $\bar{x} || \beta_{65} || \cdots || \beta_{68}$로 보내는 $65 \times 65$ 행렬 $C^{N_0}$를 구한 뒤 연립방정식을 풀면 $K$의 $\mathrm{rank}(C^{N_0}) - 1$개 bit를 구할 수 있으며, 나머지 bit는 전수조사를 통해 각각의  후보로 계산한 레지스터 초깃값을 위에서 얻은 값과 대조하여 복구할 수 있다. 최종적으로 복구한 64비트 마스터키는 다음과 같다:

$K = [0, 0, 1, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 1, 1, 0, 0, 0, 1, 0, 1, 0, 0, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 1, 1, 0, 0, 1, 1, 1, 1, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0, 1, 0, 1, 0, 0, 0]$

이로부터 생성된 keystream을 문제에서 주어진 암호문에 XOR해본 결과 error bit 2개의 위치는 각각 ct[1][13] 및 ct[12][32]이며, 복구한 평문 내용은 다음과 같다:

**Congratulations. You have solved this challenge. The GEO-Mobile Radio Interface-1 (GMR-1) is a satellite communication standard used in Thuraya, a United Arab Emirates-based regional mobile satellite service provider. The specification of the encryption algorithm used in GMR-1 was not disclosed unti**
