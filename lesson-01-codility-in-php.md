# Lesson 01: Codility in PHP

## Lesson 1

### Iterations

#### BinaryGap

* Test results: [https://app.codility.com/demo/results/trainingPXJHE7-MXF/](https://app.codility.com/demo/results/trainingPXJHE7-MXF/)

```=php
<?php
function solution($N) {
    // 將 N 轉成二進制字串
    $binary = decbin($N);

    $maxGap = 0;    // 最長 gap 長度
    $currGap = 0;   // 目前 gap 長度
    $meetOne = false; // 是否已遍歷過 1

    // 逐字元掃描
    for ($i = 0; $i < strlen($binary); $i++) {
        if ($binary[$i] == 1) {
            // 遇到 1 時，若已經在 gap 中，就更新 gap 長度
            if ($meetOne) {
                $maxGap = max($maxGap, $currGap);
            }
            // 重置並準備開始計算下一個 gap
            $meetOne = true;
            $currGap = 0;
        } else {
            // 只有在遇到第一個 1 之後，才開始累加 0 的 gap 長度
            if ($meetOne) {
                $currGap++;
            }
        }
    }
    return $maxGap;
}
```

---

## Lesson 2

### Arrays

#### CyclicRotation

* Test results: [https://app.codility.com/demo/results/trainingU7YD6W-TSB/](https://app.codility.com/demo/results/trainingU7YD6W-TSB/)

```=php
<?php
function solution($A, $K) {
    // 陣列元素個數
    $n = count($A);
    // 若陣列為空或不須旋轉，就直接回傳原陣列
    if ($n == 0) {
        return $A;
    }
    
    // 將旋轉次數 K 對陣列元素個數 n 取餘數，避免不必要的旋轉
    $K %= $n;
    if ($K == 0) {
        return $A;
    }
    
    // 旋轉後的新陣列
    $result = [];

    // 先加尾部 K 個（從 n-K 開始到 n-1）
    for ($i = $n - $K; $i < $n; $i++) {
        $result[] = $A[$i];
    }

    // 再加前面 n-K 個（從 0 到 n-K-1）
    for ($i = 0; $i < $n - $K; $i++) {
        $result[] = $A[$i];
    }
    return $result;
}
```

#### OddOccurrencesInArray

* Test results: [https://app.codility.com/demo/results/trainingG3839C-26Z/](https://app.codility.com/demo/results/trainingG3839C-26Z/)

```=php
<?php
function solution($A) {
    $N = count($A);
    
    for ($i = 0; $i < $N; $i++) {
        $found = false;
        
        for ($j = 0; $j < $N; $j++) {
            if ($i !== $j && $A[$i] == $A[$j]) {
                $found = true;
                break; // 若配對成功，就跳出
            }
        }
        
        if (!$found) {
            return $A[$i]; // 若未找到配對，就回傳答案
        }
    }
}
```

---

## Lesson 3

### Time Complexity

#### FrogJmp

* Test results: [https://app.codility.com/demo/results/trainingU4Q62W-WG6/](https://app.codility.com/demo/results/trainingU4Q62W-WG6/)

```=php
<?php
function solution($X, $Y, $D) {
    // 若起始位置 X 已經大於或等於目標位置 Y，就不需要跳躍
    if ($X >= $Y) {
        return 0;
    }

    // 計算從起始位置到目標位置的距離
    $distance = $Y - $X;

    // 先計算至少需要跳幾次
    $jump = (int)($distance / $D);
    
    // 若目標位置距離無法被跳躍距離整除，表示還需要再跳一次
    if ($distance % $D !== 0) {
        $jump++;
    }

    // 回傳所需的最小跳躍次數
    return $jump;
}
```

#### PermMissingElem

* Test results: [https://app.codility.com/demo/results/training5SBGSX-UAZ/](https://app.codility.com/demo/results/training5SBGSX-UAZ/)

```=php
<?php
function solution($A) {
    $n = count($A);
    // 先找出 1 到 N+1 完整的和
    $total = ($n + 1) * ($n + 2) / 2;
    // 陣列元素和
    $sumA = array_sum($A);
    // 差值即為遺失的元素
    return (int) ($total - $sumA);
}
```

#### TapeEquilibrium

* Test results: [https://app.codility.com/demo/results/training9BAWPW-QVY/](https://app.codility.com/demo/results/training9BAWPW-QVY/)

```=php
<?php
function solution($A) {
    $total = array_sum($A);  // 計算陣列元素和
    $left = $A[0];  // 從最左側開始，所以將最左側的一個元素作為左側和
    $right = $total - $left;  // 再把右側剩餘的元素加總作為右側和
    // 先計算 P=1 的差值
    $minDiff = abs($left - $right);
    
    $n = count($A);  // 陣列長度
    // 從 P=2 開始往後計算
    for ($i = 1; $i < $n - 1; $i++) {
        $left += $A[$i];
        $right = $total - $left;
        $tempDiff = abs($left - $right);
        if ($tempDiff < $minDiff) {
            $minDiff = $tempDiff;
        }
    }
    return $minDiff;
}

```

---

## Lesson 4

### Counting Elements

#### FrogRiverOne

* Test results: [https://app.codility.com/demo/results/trainingQA8KZ9-FES/](https://app.codility.com/demo/results/trainingQA8KZ9-FES/)

```=php
<?php
function solution($X, $A) {
    // 建立一個空陣列，用來記錄哪些位置已經有葉子
    $pos = [];

    // 記錄已被覆蓋（未重複）的位置數
    $covered = 0;

    // 逐秒檢查每一片葉子落下的位置
    for ($i = 0; $i < count($A); $i++) {
        $currPos = $A[$i];
        
        // 若該位置第一次出現葉子
        if (!isset($pos[$currPos])) {
            // 標示這個位置已經有葉子
            $pos[$currPos] = true;
            
            // 已被覆蓋（未重複）的位置數 +1
            $covered++;
            
            // 當從 1 到 X 所有位置都被覆蓋，就可以過河
            if ($covered == $X) {
                return $i; // 回傳秒數
            }
        }
    }
    // 遍歷完整個陣列後，還是無法覆蓋所有 1 到 X 的位置
    return -1;
}
```

#### PermCheck

* Test results: [https://app.codility.com/demo/results/training3VJ3PQ-SWD/](https://app.codility.com/demo/results/training3VJ3PQ-SWD/)

```=php
<?php
function solution($A) {
    $N = count($A);
    $temp = [];

    for ($i = 0; $i < $N; $i++) {
        $num = $A[$i];

        // 不是 Permutation：數字超出 1 到 N 的範圍
        if ($num < 1 || $num > $N) {
            return 0;
        }

        // 不是 Permutation：若已經出現過，表示數字重複了
        if (isset($temp[$num])) {
            return 0;
        }

        // 將該數字記錄為已遍歷過
        $temp[$num] = true;
    }
    // 若所有數字都在範圍內且未重複，則是 Permutation
    return 1;
}
```

#### MaxCounters

* Test results: [https://app.codility.com/demo/results/training7F7NQA-CUJ/](https://app.codility.com/demo/results/training7F7NQA-CUJ/)

```=php
<?php
function solution($N, $A) {
    // 初始化 N 個 counter，每個都為 0
    $counters = [];
    for ($i = 0; $i < $N; $i++) {
        $counters[$i] = 0;
    }

    // 記錄目前所有 counter 中的最大值
    $currentMax = 0;

    // 記錄上次執行 max counter 後，所有 counter 要有的最小值
    $lastUpdate = 0;

    for ($i = 0; $i < count($A); $i++) {
        $X = $A[$i];

        // 若 1 ≤ X ≤ N，執行 increase(X)
        if ($X >= 1 && $X <= $N) {
            $index = $X - 1; // 將 X 轉換為 index

            // 若該 counter 尚未更新至 lastUpdate，則更新其值
            if ($counters[$index] < $lastUpdate) {
                $counters[$index] = $lastUpdate;
            }

            // 將該 counter 加 1
            $counters[$index]++;

            // 若這次增加後比目前最大值還大，同步更新目前最大值
            if ($counters[$index] > $currentMax) {
                $currentMax = $counters[$index];
            }

        } elseif ($X == $N + 1) {
            // 若執行 max counter，就記錄目前最大值為基準，暫緩更新
            $lastUpdate = $currentMax;
        }
    }

    // 更新所有尚未同步的值
    for ($i = 0; $i < $N; $i++) {
        if ($counters[$i] < $lastUpdate) {
            $counters[$i] = $lastUpdate;
        }
    }
    return $counters;
}
```

#### MissingInteger

* Test results: [https://app.codility.com/demo/results/trainingMS634Q-VZ6/](https://app.codility.com/demo/results/trainingMS634Q-VZ6/)

```=php
<?php
function solution($A) {
    sort($A); // 將陣列元素由小到大排序
    // 從 1 開始，找出陣列中缺少的最小正整數
    $small = 1;

    // 取得陣列長度
    $count = count($A);

    for ($i = 0; $i < $count; $i++) {
        // 如果當前元素等於預期要找的最小正整數
        // 則將 small 向上遞增，繼續找下一個
        if ($A[$i] == $small) {
            $small++;
        }
        // 若當前元素大於 small
        // 表示 small 不存在於陣列中，可以直接跳出迴圈
        elseif ($A[$i] > $small) {
            break;
        }
        // 若當前元素小於 small（可能是負數或重複值），則繼續檢查
    }
    // 回傳缺少的最小正整數
    return $small;
}
```

## Lesson 5

### Prefix Sums

#### PassingCars

* Test results: [https://app.codility.com/demo/results/trainingRSNGP7-RFN/](https://app.codility.com/demo/results/trainingRSNGP7-RFN/)

```=php
<?php
function solution($A) {
    $eastCar = 0;  // 目前出現的向東車輛數
    $carPair = 0;  // 交會車輛配對數
    
    foreach ($A as $car) {
        if ($car == 0) {
            // 遇到向東車輛
            $eastCar++;
        } else {
            // 遇到向西車輛，就和前面所有向東車輛進行配對
            $carPair += $eastCar;
            if ($carPair > 1000000000) {
                return -1;
            }
        }
    }
    return $carPair;
}
```

#### CountDiv

* Test results: [https://app.codility.com/demo/results/trainingDHY34M-WCC/](https://app.codility.com/demo/results/trainingDHY34M-WCC/)

```=php
<?php
function solution($A, $B, $K) {
    // 計算 0 到 B 之間可被 K 整除的數
    $countB = (int)($B / $K);
        
    // 計算 0 到 A-1 之間可被 K 整除的數
    if ($A > 0) {
        // 相減得到 [A..B] 之間的可整除數
        $countA = (int)(($A - 1) / $K);
        return $countB - $countA;
    } else {
        $countB++;
        return $countB;
    }
}
```

#### GenomicRangeQuery

* Test results: [https://app.codility.com/demo/results/trainingRXTXV4-FB3/](https://app.codility.com/demo/results/trainingRXTXV4-FB3/)

```=php
<?php
function solution($S, $P, $Q) {
    $N = strlen($S);  // DNA 長度
    $M = count($P);   // 查詢數量

    // 初始化用來記錄從開頭到特定 index 為止出現多少次 'A', 'C', 'G' 的陣列
    // 每個陣列長度都是 N + 1 以方便處理邊界
    $a = $c = $g = array_fill(0, $N + 1, 0);
    
    // 遍歷 DNA 序列中的每一個字元
    // 建立 prefix sum：第 i+1 個位置儲存前 i 個字元的累計資訊
    for ($i = 0; $i < $N; $i++) {
        $a[$i + 1] = $a[$i];
        $c[$i + 1] = $c[$i];
        $g[$i + 1] = $g[$i];

        if ($S[$i] === 'A') {
            $a[$i + 1]++;
        } elseif ($S[$i] === 'C') {
            $c[$i + 1]++;
        } elseif ($S[$i] === 'G') {
            $g[$i + 1]++;
        }
        // 'T'
    }

    $result = [];
    
    // 開始查詢
    for ($k = 0; $k < $M; $k++) {
        $start = $P[$k];  // 起點
        $end = $Q[$k] + 1;  // 終點
        
        // 檢查這段區間內是否包含 A (impact factor = 1)
        if ($a[$end] - $a[$start] > 0) {
            $result[] = 1;
        // 檢查是否包含 C (impact factor = 2)
        } elseif ($c[$end] - $c[$start] > 0) {
            $result[] = 2;
        // 檢查是否包含 G (impact factor = 3)
        } elseif ($g[$end] - $g[$start] > 0) {
            $result[] = 3;
        // 若均無包含 A、C、G，則即為 T (impact factor = 4)
        } else {
            $result[] = 4;
        }
    }
    return $result;
}
```

#### MinAvgTwoSlice

* Test results: [https://app.codility.com/demo/results/training2ZB32D-8JS/](https://app.codility.com/demo/results/training2ZB32D-8JS/)

```=php
<?php
function solution($A) {
    // 初始化：先假設最小平均值是第 0 和 1 這兩個元素的平均
    $minIndex = 0;
    $minAvg = ($A[0] + $A[1]) / 2;
    
    $N = count($A); // 陣列長度
    // 從 index 0 開始遍歷至倒數第 2 個元素
    for ($i = 0; $i < $N - 1; $i++) {
        // 1. 檢查長度為 2 的 slice (i, i+1)
        $avg2 = ($A[$i] + $A[$i + 1]) / 2;
        if ($avg2 < $minAvg) {
            $minAvg = $avg2;
            $minIndex = $i;
        }

        // 2. 檢查長度為 3 的 slice (i, i+1, i+2)
        if ($i < $N - 2) {
            $avg3 = ($A[$i] + $A[$i + 1] + $A[$i + 2]) / 3;
            if ($avg3 < $minAvg) {
                $minAvg = $avg3;
                $minIndex = $i;
            }
        }
    }

    // 回傳平均值最小的 slice 起始 index
    return $minIndex;
}
```

## Lesson 6

### Sorting

#### Distinct

* Test results: [https://app.codility.com/demo/results/trainingUK5K8N-8EW/](https://app.codility.com/demo/results/trainingUK5K8N-8EW/)

```=php
<?php
function solution($A) {
    $num = []; // 記錄出現過的數值

    // 遍歷陣列 $A
    for ($i = 0; $i < count($A); $i++) {
        $idx = $A[$i];  // 取得當前的 index
        $num[$idx] = true; // 將該值設為 key，表示已出現過
    }

    // 回傳不同 key 的數量，也就是不同數值的個數
    return count($num);
}
```

#### MaxProductOfThree

* Test results: [https://app.codility.com/demo/results/trainingRW42TU-S8Q/](https://app.codility.com/demo/results/trainingRW42TU-S8Q/)

```=php
<?php
function solution($A) {
    sort($A); // 先排序整個陣列

    $n = count($A);
    
    $max1 = $A[$n - 1] * $A[$n - 2] * $A[$n - 3]; // 最大三個數相乘
    $max2 = $A[0] * $A[1] * $A[$n - 1];           // 最小三個數相乘(可能有負數的情況)

    return max($max1, $max2);
}
```

#### Triangle

* Test results: [https://app.codility.com/demo/results/trainingJZBMFD-UNX/](https://app.codility.com/demo/results/trainingJZBMFD-UNX/)

```=php
<?php
function solution($A) {
    $n = count($A);
    if ($n < 3) {
        return 0;
    }

    sort($A); // 先排序整個陣列
    
    // 任意兩邊長度和大於第三邊長度
    for ($i = 0; $i < $n - 2; $i++) {
        if ($A[$i] > $A[$i + 2] - $A[$i + 1]) {
            return 1;
        }
    }
    return 0;
}
```

#### NumberOfDiscIntersections

* Test results: [https://app.codility.com/demo/results/training8ZK86S-NZ2/](https://app.codility.com/demo/results/training8ZK86S-NZ2/)

```=php
<?php
function solution($A) {
    $N = count($A); // 圓的數量

    // 建立 start 和 end 兩個陣列來記錄每個圓的左/右邊界
    $start = []; // 每個圓的左邊界(中心 - 半徑)
    $end = [];   // 每個圓的右邊界(中心 + 半徑)

    for ($i = 0; $i < $N; $i++) {
        $start[] = $i - $A[$i]; // 計算第 i 個圓的左邊界
        $end[]   = $i + $A[$i]; // 計算第 i 個圓的右邊界
    }

    // 將左右邊界排序以方便比較交集，
    sort($start); // 將所有圓的左邊界由小到大排序
    sort($end);   // 將所有圓的右邊界由小到大排序

    $count = 0; // 記錄交集的數

    // 檢查圓是否有交集
    for ($i = 0; $i < $N; $i++) {
        // 只需檢查在 i 之後的圓以避免重複計算
        for ($j = $i + 1; $j < $N; $j++) {
            // 如果第 j 個圓盤的左邊界 <= 第 i 個圓的右邊界，表示兩圓有交集
            if ($start[$j] <= $end[$i]) {
                $count++;

                // 若交集數超過 10,000,000，則根據題目要求直接回傳 -1
                if ($count > 10000000) {
                    return -1;
                }
            } else {
                // 因為 start 是排序過的，所以後面的圓不可能發生交集
                break;
            }
        }
    }
    // 回傳總交集數
    return $count;
}
```

## Lesson 7

### Stacks and Queues

#### Brackets

* Test results: [https://app.codility.com/demo/results/trainingXSWMWR-DW8/](https://app.codility.com/demo/results/trainingXSWMWR-DW8/)

```=php
<?php
function solution($S) {
    // 將輸入的字串拆解成字元陣列
    $string = str_split($S);

    // 建立一個 stack 來記錄括號
    $stack = [];

    // 遍歷字元陣列
    for ($i = 0; $i < count($string); $i++) {
        $char = $string[$i];

        // 若是左括號，則加入 stack
        if ($char === '{' || $char === '[' || $char === '(') {
            array_push($stack, $char);
        }
        // 若是右括號，則檢查 stack 最上層是否為對應的左括號
        else if ($char === '}') {
            // 若不相符或 stack 為空，則回傳 0
            if (array_pop($stack) !== '{') {
                return 0;
            }
        } else if ($char === ']') {
            if (array_pop($stack) !== '[') {
                return 0;
            }
        } else if ($char === ')') {
            if (array_pop($stack) !== '(') {
                return 0;
            }
        }
    }

    // 最後檢查 stack 是否為空，表示所有括號已完全配對
    if (empty($stack)) {
        return 1; // 配對成功
    } else {
        return 0; // 尚有未配對的括號
    }
}
```

#### Fish

* Test results: [https://app.codility.com/demo/results/trainingANC22S-U5Z/](https://app.codility.com/demo/results/trainingANC22S-U5Z/)

```=php
<?php
function solution($A, $B) {
    $N = count($A);
    $down = []; // 儲存下游魚的 stack
    $remain = 0;  // 存活的魚數

    for ($i = 0; $i < $N; $i++) {
        $size = $A[$i];
        $dir = $B[$i];

        if ($dir == 1) {
            // 下游魚推入 stack
            array_push($down, $size);
        } else {
            // 交戰判斷
            while (!empty($down)) {
                // 上游魚和 stack 頂端的下游魚交戰
                if ($down[count($down) - 1] > $size) {
                    // 上游魚輸了
                    break;
                } else {
                    // 上游魚贏了
                    array_pop($down);
                }
            }
            if (empty($down)) {
                // 若沒有遇到下游魚，則上游魚存活
                $remain++;
            }
        }
    }
    // 最後還存活的下游魚 + 上游魚
    return $remain + count($down);
}
```

#### Nesting

* Test results: [https://app.codility.com/demo/results/trainingCZNX4F-JUF/](https://app.codility.com/demo/results/trainingCZNX4F-JUF/)

```=php
<?php
function solution($S) {
    $stack = [];
    
    for ($i = 0; $i < strlen($S); $i++) {
        $char = $S[$i];

        if ($char === '(') {
            array_push($stack, $char); // 推入左括號
        } else {
            if (empty($stack)) {
                // 沒有對應的左括號
                return 0;
            } else {
                array_pop($stack); // 彈出一個左括號作為配對
            }
        }
    }

    // 若 stack 為空，則表示所有括號都有正確配對
    if (empty($stack)) {
        return 1;
    } else {
        return 0;
    }
}
```

#### StoneWall

* Test results: [https://app.codility.com/demo/results/training5R2NWR-R2M/](https://app.codility.com/demo/results/training5R2NWR-R2M/)

```=php
<?php
function solution($H) {
    $stack = [];
    $count = 0;
    $n = count($H);

    for ($i = 0; $i < $n; $i++) {
        $height = $H[$i];

        // 若當前高度小於 stack 頂部石塊，則彈出石塊直到高度不大於當前高度的情形
        while (!empty($stack) && end($stack) > $height) {
            array_pop($stack);
        }

        // 若 stack 為空或頂部高度不等於當前高度，則推入一個石塊
        if (empty($stack) || end($stack) < $height) {
            array_push($stack, $height);
            $count++;
        }
    }
    return $count;
}
```
