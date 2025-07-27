# 取り組み方

- step1: 5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず 5 分立ったら答えを見て、全て消して書き直す。ゴールは答えを送信して正解するまで。
- step2: コードを読みやすくするようにできるだけ整える。動くコードになったら OK。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。
- step4: レビューのフィードバックを反映したコードを書く。

# step1

- 問題が答えとして何を求めているのか、C++ の場合どのように表現すれば良いのかなどを調べつつ実装したため、5 分以上かかってしまいました。
  - ブルートフォース的な 2 重ループで解く方法しか思いつきませんでした。
- 計算量について詳しくなかったため、調査しました。今回はループが 2 つなので、計算量としは O(n^2) になると思います。
  - ref: [計算量オーダーの求め方を総整理！ 〜 どこから log が出て来るか 〜](https://qiita.com/drken/items/872ebc3a2b5caaa4a0d0)
- 念の為、最後に runtime_error を入れました。問題の「You may assume that each input would have exactly one solution」から必ず 1 つの解が存在するため、本来はこのような対応は不要ですが、イレギュラーなケースを想定して入れました。また、関数を使用する人（他者 or 未来の自分）が関数の中の実装まで見るかどうか定かではないため、問題があった場合に気付けるようにという意味でも入れています。

```cpp
class Solution {
public:
  vector<int> twoSum(vector<int> &nums, int target) {
    for (int i = 0; i < nums.size(); i++) {
      for (int j = 0; j < nums.size(); j++) {
        if (i == j) {
          continue;
        }

        if (nums[i] + nums[j] == target) {
            return {i, j};
        }
      }
    }

    throw runtime_error("No two sum solution found");
  }
};
```

- 先に a = target - b を行い、a を hash map に入れておくことで 1 回のループで済むことがわかったので、その方法で実装しなおしました。
- C++ には map と unordered_map の 2 種類あり、今回はキーの順番を担保する必要がないため、速度の速い unordered_map を使用しました。
- 計算量としては O(n) になり、少なくなりました。今回の nums が `2 <= nums.length <= 10^4` のため、10^8 よりも少ないため、10^4 だったとしても現在のマシンであれば 1 秒以内で処理が可能だと考えられます。

```cpp
class Solution {
public:
  vector<int> twoSum(vector<int> &nums, int target) {
    unordered_map<int, int> m;

    for (int i = 0; i < nums.size(); i++) {
      int want = target - nums[i];

      if (m.count(want)) {
        return {i, m[want]};
      }

      m[nums[i]] = i;
    }

    throw runtime_error("No two sum solution found");
  }
};
```

# step2

- AI と壁打ちし、いくつか改善点がわかりました。
  - C++20 で contains が追加されました。
    - 存在確認と要素の取得が必要であれば、find() を使うと良いです。
  - C++ では const を使用すれば、イミュータブルにできます。

```cpp
class Solution {
public:
  vector<int> twoSum(const vector<int> &nums, int target) {
    unordered_map<int, int> m;

    for (int i = 0; i < nums.size(); i++) {
      int want = target - nums[i];

      if (m.contains(want)) {
        return {i, m[want]};
      }

      m[nums[i]] = i;
    }

    throw runtime_error("No two sum solution found");
  }
};
```

# step3

- 3 回目のコードを載せます。

```cpp
class Solution {
public:
  vector<int> twoSum(const vector<int> &nums, int target) {
    unordered_map<int, int> map;

    for (int i = 0; i < nums.size(); i++) {
      int want = target - nums[i];

      if (map.contains(want))
        return {i, map[want]};

      map[nums[i]] = i;
    }

    throw runtime_error("No two sum solution found!");
  }
};
```

# step4

- レビューのフィードバックを反映したコードを載せます。

```cpp
class Solution {
public:
  vector<int> twoSum(const vector<int> &nums, int target) {
    unordered_map<int, int> map;

    for (int i = 0; i < nums.size(); i++) {
      int complement = target - nums[i];

      if (map.contains(complement))
        return {i, map[complement]};

      map[nums[i]] = i;
    }

    throw runtime_error("No two sum solution found!");
  }
};
```
