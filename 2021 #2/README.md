# 2021 Crypto Analysis Contest #2 Solution

먼저 '출제 의도상 모범 답안으로 추정되는 답안'은 [이 링크](https://hashmm.com/post/crypto-contest-2021-writeup/index.html)에서 확인할 수 있다.

3.5-round distinguisher를 이용한 $k'_0 \oplus k_1$ 복구 단계까지는 링크의 풀이와 동일하게 진행되므로 $k_1$ 복구 과정만 살펴보도록 하자.

[기존 논문](https://eprint.iacr.org/2015/245.pdf)에서 사용된 2.5-round distinguisher는 4개의 8-quasiactive nibble을 가진 평문 set에서 출발했을 때 모든 nibble이 balanced된 상태에 도달하는지를 확인하는 방식이다.

<img width="600" height="240" alt="2 5round" src="https://github.com/user-attachments/assets/dabfc398-37d3-4fbf-aed5-37cf236c6b96" />

하지만 주어진 pt.dat에 조건을 만족하는 평문 set이 없으므로, 여기서는 대신 1개의 active nibble을 가진 평문 set에서 출발했을 때 4개의 8-quasiactive nibble과 12개의 4-quasiactive nibble을 갖는 상태에 도달하게 됨을 이용한다.
즉 3.5-round distinguisher를 사용한 상태에서 middle round를 추가로 peel off했을 때 4개의 8-quasiactive nibble과 12개의 4-quasiactive nibble이 정해진 위치에 맞게 나타나는지를 확인하면 된다.

<img width="600" height="240" alt="rowbyrow" src="https://github.com/user-attachments/assets/4d93166c-aad1-49d5-8f5c-eb747a44331e" />

이때 $S$와 $S^{-1}$는 자명하게 nibble-independent하며, $M'$은 block diagonal matrix로 정의된 형태를 보면 row-independent함을 알 수 있다.

<img width="600" height="270" alt="m" src="https://github.com/user-attachments/assets/ee2000e3-ca1b-4956-8afc-f381379f967f" />

4bit (nibble)와 16bit (row)의 최소공배수는 16bit이므로 middle round인 $S \circ M' \circ S^{-1}$ 또한 row-independent하다.

**따라서 16bit씩 나눠서 brute force를 돌려도 아무 문제가 없다!**

즉 모범 답안에서의 key space 크기인 $2^{32}$보다도 더욱 작은 $4 \times 2^{16}$번의 탐색만으로 $k_1$을 복구하는 것이 가능하다.
(만약 key가 알파벳 소문자로만 구성되어 있다는 힌트까지 사용했다면 필요한 탐색 횟수를 $4 \times 2^{8}$번 이내로 줄일 수 있었을 것이다.)

최종적으로 복구한 $k_1$ 값은 **(16진수)** 로, ASCII로 변환하면 링크된 답안의 결과값과 일치하는 문자열이 나온다.
