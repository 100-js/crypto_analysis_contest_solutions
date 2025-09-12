# 2025 Crypto Analysis Contest #5 Solution

이하에서 Alice의 개인키는 $d_A$, Bob의 개인키는 $d_B$로 표기하였다.

먼저 $P_{shared} = d_{A}(d_{B}G)$의 $(x, y)$ 값은 다음과 같다.

<table>
  <tr>
    <td>
      x
    </td>
    <td>
      0077e4b10dd38932457fd96eb1852129f09c1c0ed2973b09e1b8d8b8caf95da0bdcd99d744a0e4f84734e3a1aed4562ed376b3689beedff67d0b42b415e743608a19
    </td>
  </tr>
  <tr>
    <td>
      y
    </td>
    <td>
      0156ece822bc48e444e8e8435c8f7ba66da953c82e6629b3f7456566c5f61fb88b65a9d4cea62299bd1b3d7e652382b8c1afcbe938a04abb57108116ffe83d172d0a
    </td>
  </tr>
</table>

관찰을 통해 Bob의 공개키 $d_{B}G$가 원래의 곡선 $E$가 아닌 상수항이 누락된 곡선 $E': \text{ } y^2 = x^3 - 3x \text{ } (\mathrm{mod} \text{ } p)$ 위의 점임을 알 수 있으며, $P_{shared}$ 또한 $d_{B}G + \cdots + d_{B}G$이므로 $E'$ 위의 점임이 계산으로 검증된다.

이때 $p \equiv 3 \text{ } (\mathrm{mod} \text{ } 4)$이므로 $E'$은 supersingular curve, 즉 [MOV attack을 통해 DLP를 푸는 것이 가능하다.](https://eprint.iacr.org/2019/400.pdf)

![Degenerate_Fault_Attacks_on_Elliptic_Curve_Parameters_in_OpenSSL.png](https://github.com/user-attachments/assets/3cd13588-a407-4ed6-8ae8-053ae1dbc0f3)

[Github에서 가져온 MOV attack 소스코드](https://github.com/jvdsn/crypto-attacks/blob/master/attacks/ecc/mov_attack.py)를 활용하여 복구한 $d_A$ 값은 다음과 같다:
**6636353156423030954015460281173207616818947356563557016949415787028983784744206133554905384474990610636203706728731813205732132665247763852710402389523578039**
