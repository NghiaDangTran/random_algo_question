# facebook random_algo_question 

A group of K miners discovered a trunk of N money notes while mining a remote mountain. Each money note has a value X. Since they all work together, they would like to divide the money found to all of them as evenly as possible. You are asked to write a function to help those miners to divide the notes to each of them so that the difference of the one receiving the highest amount of money and the one receiving the lowest is the smallest. 
Example: K = 2 miners, N = 5 notes: (3, 6, 15, 16, 21) - The minimal difference is $1 - Miner 1: 3+6+21 = $30, Miner 2: 15+16 = $31


# Tóm tắc câu hỏi:
- cho một số K 
- và một dãy aray N
- làm sao chia tất cả số N sao cho K max - k Min = min 


# phân tích đề:

1. làm sao chia tất cả số có mặt trong N cho K người
2. làm sao cho K max - k Min = min

# giải quyết từng vấn đề:

1. làm sao chia tất cả số có mặt trong N cho K người
  - thì cứ chia thôi, một vòng lặp từng người 1 số trong N đến khi hết
2. làm sao cho K max - k Min = min
  - không biết làm sao, hay là cứ cộng đại mội người 1 số như 1,  thử
    
```
def distribute_notes(K, N):
    # Sort the notes in descending order
    N.sort()

    # Initialize the total value each miner has
    miners = [0]*K

    # Distribute the notes
    curr=0
    while N:
        # give each miner a note 
        note=N.pop()
        miners[curr%K] += note
        curr+=1

    return miners
K = 2
N = [3,6,15,16,21]
print(distribute_notes(K, N))
# return [39, 22]
```

ok cái này chưa giải quyết dc 2 mà ít nhất mình đã chia điều hết tất cả các N ( được 0.1 điểm rồi :)) )

# cải thiện lại algo

cái K max - k Min nghe cứ có chử Min với Max hay thì thử add hàm min hay max vào thử 
ở mổi lần cộng giá trị note vào từng miner thì mình thử chỉ cộng cho thằng nào đang có ít nhất thôi vì theo logic thì chia hết tiền một lượt thì cái lượt tiếp theo thằng có ít tiền nhất nên được cộng trước chứ không thôi chẳng lẽ ông nhiều tiền nhất được cộng mãi (khôn như em quê anh đầy)


```
def distribute_notes(K, N):
    # Sort the notes in descending order
    N.sort()

    # Initialize the total value each miner has
    miners = [0]*K

    # Distribute the notes
    while N:
        # give each miner a note 
        note=N.pop()
        min_miner = miners.index(min(miners))
        miners[min_miner] += note


    return miners
K = 2
N = [3,6,15,16,21]
print(distribute_notes(K, N))
# return [30, 31]

```
cái này hơi được nha gap giữa hai số nhỏ lại một chút mà vẫn còn xa so với kết quả dự trong function cuối có khả năng hàm Min có mặt

# tiếp tục của thiện

nhìn lại sample answer họ giải thích vầy

>  Miner 1: 3+6+21 = $30, Miner 2: 15+16 = $31

sao nó đi từ 3->6->21 mà không đi tiếp tới 15, khả năng cao nó lựa cái để chọn số nào dựa vào điều kiện gì đó
</br>
giả sử mình đừng care tới K max - k Min = min  mà làm thành chia điều thì ta có 

```
sum(N)/K
# return 30.5
```
số này hơi kì, sao nó gần với lại số 30 với 31 quá, mà cũng có lý, khi mà trường hợp chia tiền bằng nhau thì ra cí  K max - k Min == 0, suy ra sum(N)/K phải có mặt trong func cuối


# tóm tắc 2
sau khi mình test thì khả năng cao hàm min (hoạc max) và sum(N)/k sẽ có trong function cuối
## tiếp tục của thiện
mà nếu theo tóm tắc 2 thì ta có thể viết lại bài toán như sau
1. làm sao chia tất cả số có mặt trong N cho K người
2. làm sao cho mỗi người có số tiên min hoặc max sao đó với sum(N)/k

tới đây bí phải google (hoặc bạn đã từng gặp dạng này rồi), một bài toàn rất nổi tiếng tên là Knapsack Problem cũng hơi cùng nguyên lý, dùng Greedy Algorithms
với cái mỗi đứa miner mình cần pick so cho nó gần với cái số sum(N)/k là được (giải thích để sau) sau đó thì cứ cộng đến khi miner đÓ >=  sum(N)/k

cuối cùng dư bao nhiêu thì thằng cuối hưởng 


```
def distribute_notes(K, N):
    N.sort()
    # Calculate the target value each miner should receive
    target = sum(N) / K

    # Initialize the total value each miner has
    miners = [0]*K
    miner_notes = [[] for _ in range(K)]  # To keep track of notes each miner has

    # While there are still notes left to distribute
    while N:
        # For each miner
        for i in range(K):
            # While the miner has not reached the target value
            while miners[i] < target and N:
                # Find the note closest to the target value
                min_diff = float('inf')
                closest_note = None
                for note in N:
                    diff = abs(target - (miners[i] + note))
                    if diff < min_diff:
                        min_diff = diff
                        closest_note = note
                
                N.remove(closest_note)
                # And add the note to this miner's total
                miners[i] += closest_note
                # Add the note to the miner's list of notes
                miner_notes[i].append(closest_note)
                if miners[i] + closest_note >= target:
                    break
                # Otherwise, remove this note from the list of available notes
                
    # Distribute the remaining notes to the last miner
    while N:
        note = N.pop()
        miners[-1] += note
        miner_notes[-1].append(note)
    print("số tiền mỗi miner ", miners,min(miners)-max(miners) ,miner_notes)
    print("số tiền chênh lệch ",max(miners)-min(miners))
    print("các tờ tiền đã dùng cho mỗi miner ", miner_notes)

K = 2
N = [3,6,15,16,21]
distribute_notes(K, N)
#số tiền mỗi miner  [30, 31] -1 [[21, 6, 3], [16, 15]]
#số tiền chênh lệch  1
#các tờ tiền đã dùng cho mỗi miner  [[21, 6, 3], [16, 15]]
K = 3
N = [1, 2, 3, 4, 5, 6, 7, 8, 9]
distribute_notes(K, N)
#số tiền mỗi miner  [15, 15, 15] 0 [[9, 5, 1], [8, 4, 3], [7, 6, 2]]
#số tiền chênh lệch  0
#các tờ tiền đã dùng cho mỗi miner  [[9, 5, 1], [8, 4, 3], [7, 6, 2]]
K = 3
N = [3, 3, 3, 3, 3, 3, 3, 3, 3]
distribute_notes(K, N)
#số tiền mỗi miner  [9, 9, 9] 0 [[3, 3, 3], [3, 3, 3], [3, 3, 3]]
#số tiền chênh lệch  0
#các tờ tiền đã dùng cho mỗi miner  [[3, 3, 3], [3, 3, 3], [3, 3, 3]]
K = 4
N = [1, 2, 3, 4, 5,2,2]
distribute_notes(K, N)
#số tiền mỗi miner  [5, 5, 5, 4] -1 [[5], [4, 1], [3, 2], [2, 2]]
#số tiền chênh lệch  1
#các tờ tiền đã dùng cho mỗi miner  [[5], [4, 1], [3, 2], [2, 2]]
```

# kết quả
chưa biết kết quả thuật toán đuungs không ,mình ra được kết quả đúng so với sample được 0.5+0.1 giải thích chắc tổng chắc đưuọc 0.6 :))



# extra: thằng gpt viết (mai mốt học thêm algo thì phải chứng minh được algo mình maybe ra kết quả đúng)


To prove the greedy algorithm for this problem, let's break down the problem and the algorithm's steps.

# Problem Statement: 
The goal is to distribute the notes to the miners in such a way that the difference between the miner who receives the most and the miner who receives the least is as small as possible. This is equivalent to saying that we want to make the distribution of notes as even as possible.

# Algorithm Steps:

- Calculate the target value for each miner, which is the integer division of the total sum of notes by the number of miners (sum(N)//K).

- Initialize the total value each miner has to 0.

- While there are still notes left to distribute, for each miner, find the note that brings their total as close as possible to the target without exceeding it. Add this note to the miner's total and remove it from the list of available notes.

If any notes are left after all miners have reached the target value, give them to the last miner.

# Proof:

The greedy algorithm works by making the locally optimal choice at each stage with the hope that these local choices will lead to a global optimum. In this case, the locally optimal choice is to always choose the note that brings a miner's total as close as possible to the target without exceeding it.

This approach ensures that each miner receives an amount as close as possible to the target value before any miner receives more than the target. This is crucial for keeping the difference between the highest and lowest totals as small as possible.

Once all notes that can bring a miner's total to the target without exceeding it have been distributed, any remaining notes are given to the last miner. This ensures that all notes are distributed and doesn't increase the difference between the highest and lowest totals any more than necessary.

The algorithm cannot always guarantee the smallest possible difference, particularly when the total sum of the notes is not evenly divisible by the number of miners, or when the values of the notes vary widely. However, it generally provides a good, close-to-optimal solution for a wide range of inputs, and is much more efficient than exhaustive methods that guarantee an optimal solution.

Therefore, while the greedy algorithm for this problem does not guarantee the absolute best solution in every case, it is a viable and efficient solution that works well for a wide range of inputs.








 
