# 2021 암호분석

[Practical Attacks on the Round-reduced PRINCE.pdf](https://eprint.iacr.org/2015/245.pdf)

먼저 ‘출제 의도상 모범 답안으로 추정되는 답안’은 여기서 확인할 수 있다.

[암호 뉴비들의 암호 깨는 대회 출전기 (hashmm.com)](https://hashmm.com/post/crypto-contest-2021-writeup/index.html)

논문에서 사용된 2.5-round distinguisher는 4개의 8-quasiactive nibble을 가진 평문 set에서 출발했을 때 모든 nibble이 balanced된 상태에 도달하게 됨을 이용한다.

여기서는 대신 1개의 active nibble을 가진 평문 set에서 출발했을 때 4개의 8-quasiactive nibble과 12개의 4-quasiactive nibble을 갖는 상태에 도달하게 됨을 이용한다.

즉 3.5-round distinguisher를 사용한 상태에서 middle round를 추가로 peel off했을 때 4개의 8-quasiactive nibble과 12개의 4-quasiactive nibble을 갖는지를 확인하면 된다.

이때 $S$는 nibble-independent하며, 이는 $S^{-1}$도 마찬가지이다.

한편 $M'$은 row-independent하다.

4bit (nibble)와 16bit (row)의 최소공배수는 16bit이므로 middle round인 $S \circ M' \circ S^{-1}$ 또한 row-independent하다.

따라서 16bit씩 나눠서 brute force를 돌려도 아무 문제가 없다!

즉 모범 답안에서의 key space 크기인 $2^{32}$보다도 더욱 작은 $4 \times 2^{16}$번의 탐색만으로 $k_0$를 복구하는 것이 가능하다.

만약 key가 알파벳 소문자로만 구성되어 있다는 힌트까지 사용했다면 필요한 탐색 횟수를 $4 \times 2^{8}$번 이내로 줄일 수 있었을 것이다.
