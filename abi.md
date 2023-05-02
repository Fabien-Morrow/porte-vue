A call to a function with the signature

```
f(uint256,uint32[],bytes10,bytes)
```

with values

```
(0x123, [0x456, 0x789], "1234567890", "Hello, world!")
```

is encoded in the following way:

- the 4 arguments are encoded into a tuple

```
T = (T1, T2, T3, T4)
```

- T is dynamic because one of its element is dynamic

```
0x8be65246                                                       - function signature
0000000000000000000000000000000000000000000000000000000000000123 - head(T1), static, so value
0000000000000000000000000000000000000000000000000000000000000080 - head(T2), dynamic, so offset pointing on tail(T2) after the 4 heads
3132333435363738393000000000000000000000000000000000000000000000 - head(T3), static, so value
00000000000000000000000000000000000000000000000000000000000000e0 - head(T4), dynamic, so offset pointing on tail(T4) after the 4 heads + tail(T2)
0000000000000000000000000000000000000000000000000000000000000002 - tail(T2) - head(uint32[]), value is array size
0000000000000000000000000000000000000000000000000000000000000456 - tail(T2) - tail(uint32[]), uint32[0]
0000000000000000000000000000000000000000000000000000000000000789 - tail(T2) - tail(uint32[]), uint32[1]
000000000000000000000000000000000000000000000000000000000000000d - tail(T4) - head(bytes), value is bytes's length
48656c6c6f2c20776f726c642100000000000000000000000000000000000000 - tail(T4) - tail(bytes), value
```

---

A call to a function with the signature

```
g(uint256[][],string[])
```

with values

```
([[1, 2], [3]], ["one", "two", "three"])
```

is encoded in the following way:

- the 2 arguments are encoded into a tuple

```
T = (T1, T2)
```

- T is dynamic because one of its element is dynamic. For clarity, we use placeholders `abcdefg` for offsets right now and compute them after

```
0x2289b18c                                                            - function signature
 0 - f                                                                - head(T1), offset of [[1, 2], [3]]
 1 - g                                                                - head(T2), offset of ["one", "two", "three"]
 2 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - head(uint256[][]), count for [[1, 2], [3]]
 3 - a                                                                - tail(T1) - tail(uint256[][]) - head([1, 2]), offset of [1, 2]
 4 - b                                                                - tail(T1) - tail(uint256[][]) - head([3]), offset of [3]
 5 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - tail(uint256[][]) - tail([1, 2]), count for [1, 2]
 6 - 0000000000000000000000000000000000000000000000000000000000000001 - tail(T1) - tail(uint256[][]) - tail([1, 2]), encoding of 1
 7 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - tail(uint256[][]) - tail([1, 2]), encoding of 2
 8 - 0000000000000000000000000000000000000000000000000000000000000001 - tail(T1) - tail(uint256[][]) - tail([3]), count for [3]
 9 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T1) - tail(uint256[][]) - tail([3]), encoding of 3
10 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - head(string[]),count for ["one", "two", "three"]
11 - c                                                                - tail(T2) - tail(string[]) - head("one"), offset for "one"
12 - d                                                                - tail(T2) - tail(string[]) - head("two"), offset for "two"
13 - e                                                                - tail(T2) - tail(string[]) - head("three"), offset for "three"
14 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - tail(string[]) - tail("one"), count for "one"
15 - 6f6e650000000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("one"), encoding of "one"
16 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - tail(string[]) - tail("two"), count for "two"
17 - 74776f0000000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("two"), encoding of "two"
18 - 0000000000000000000000000000000000000000000000000000000000000005 - tail(T2) - tail(string[]) - tail("three"),count for "three"
19 - 7468726565000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("three"),encoding of "three"
```

Once offsets are computed, we obtain :

```
0x2289b18c                                                            - function signature
 0 - 0000000000000000000000000000000000000000000000000000000000000040 - head(T1), offset of [[1, 2], [3]]
 1 - 0000000000000000000000000000000000000000000000000000000000000140 - head(T2), offset of ["one", "two", "three"]
 2 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - head(uint256[][]), count for [[1, 2], [3]]
 3 - 0000000000000000000000000000000000000000000000000000000000000040 - tail(T1) - tail(uint256[][]) - head([1, 2]), offset of [1, 2]
 4 - 00000000000000000000000000000000000000000000000000000000000000a0 - tail(T1) - tail(uint256[][]) - head([3]), offset of [3]
 5 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - tail(uint256[][]) - tail([1, 2]), count for [1, 2]
 6 - 0000000000000000000000000000000000000000000000000000000000000001 - tail(T1) - tail(uint256[][]) - tail([1, 2]), encoding of 1
 7 - 0000000000000000000000000000000000000000000000000000000000000002 - tail(T1) - tail(uint256[][]) - tail([1, 2]), encoding of 2
 8 - 0000000000000000000000000000000000000000000000000000000000000001 - tail(T1) - tail(uint256[][]) - tail([3]), count for [3]
 9 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T1) - tail(uint256[][]) - tail([3]), encoding of 3
10 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - head(string[]),count for ["one", "two", "three"]
11 - 0000000000000000000000000000000000000000000000000000000000000060 - tail(T2) - tail(string[]) - head("one"), offset for "one"
12 - 00000000000000000000000000000000000000000000000000000000000000a0 - tail(T2) - tail(string[]) - head("two"), offset for "two"
13 - 00000000000000000000000000000000000000000000000000000000000000e0 - tail(T2) - tail(string[]) - head("three"), offset for "three"
14 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - tail(string[]) - tail("one"), count for "one"
15 - 6f6e650000000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("one"), encoding of "one"
16 - 0000000000000000000000000000000000000000000000000000000000000003 - tail(T2) - tail(string[]) - tail("two"), count for "two"
17 - 74776f0000000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("two"), encoding of "two"
18 - 0000000000000000000000000000000000000000000000000000000000000005 - tail(T2) - tail(string[]) - tail("three"),count for "three"
19 - 7468726565000000000000000000000000000000000000000000000000000000 - tail(T2) - tail(string[]) - tail("three"),encoding of "three"
```
