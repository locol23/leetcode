# 取り組み方

- step1:
  5分考えて分からなかったら答えを見る。答えを理解したら、答えを隠して書く。筆が進まず 5 分立ったら答えを見て、全て消して書き直す。ゴールは答えを送信して正解するまで。
- step2: コードを読みやすくするようにできるだけ整える。動くコードになったら OK。
- step3: 時間を計りながら書く。10分以内に3回連続でアクセプトされるまで。
- step4: レビューのフィードバックを反映したコードを書く。

# step1

- 1個のループの中で、それぞれの先頭から取り出していき、足した結果を新しく定義した結果の linked
  list へ追加する
- 10を越えた場合は、一桁だけ現在の linked list へ追加し、次の linked
  list の計算時に 1 を足して繰り上げを再現する
- 自力で実装できたものの、同じ処理が散らばっていたりするので、関数化する or 処理自体をもっとブラッシュアップできそうな感じを受けた

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
  ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    int added_val = l1->val + l2->val;
    bool is_carry = false;

    if (added_val > 9) {
        is_carry = true;
        added_val = added_val % 10;
    }

    ListNode* added_head_node = new ListNode(added_val);

    if (l1->next != nullptr || l2->next != nullptr) {
      added_head_node->next = new ListNode();
    }

    ListNode* added_current_node = added_head_node->next;
    ListNode* current_l1_node = l1->next;
    ListNode* current_l2_node = l2->next;

    if (l1->next == nullptr && l2->next == nullptr) {
        if (is_carry) {
            added_head_node->next = new ListNode(1);
        }

        return added_head_node;
    }

    while (added_current_node != nullptr) {
      if (current_l1_node == nullptr && current_l2_node == nullptr) {
        break;
      }

      if (current_l1_node == nullptr) {
        current_l1_node = new ListNode();
      }

      if (current_l2_node == nullptr) {
        current_l2_node = new ListNode();
      }

      int added_val = current_l1_node->val + current_l2_node->val;

      if (is_carry) {
        added_val += 1;
        is_carry = false;
      }


      if (added_val > 9) {
          is_carry = true;
          added_val = added_val % 10;
      }

      added_current_node->val = added_val;

      if (current_l1_node->next != nullptr || current_l2_node->next != nullptr) {
        if (current_l1_node->next == nullptr) {
            current_l1_node->next = new ListNode(0);
        }

        if(current_l2_node->next == nullptr) {
            current_l2_node->next = new ListNode(0);
        }

        added_current_node->next = new ListNode();
      }

      if (current_l1_node->next == nullptr && current_l2_node->next == nullptr && is_carry) {
        added_current_node->next = new ListNode(1);
      }

      added_current_node = added_current_node->next;
      current_l1_node = current_l1_node->next;
      current_l2_node = current_l2_node->next;
    }

    return added_head_node;
  }
};
```

# step2

- dummy_head を準備したことによって、while の中で同じ用に処理ができるようになったので、ロジックを while の中だけにすることができた
- 最後に dummy_head を削除することによって、メモリリークを防ぐためにメモリ解放を行っている
  - パフォーマンス的には、dummy_head を削除しない方が数 ms 処理は速い

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy_head = new ListNode(0);
        ListNode* current = dummy_head;
        int carry = 0;

        while(l1 != nullptr || l2 != nullptr || carry != 0) {
            int val1 = (l1 != nullptr) ? l1->val : 0;
            int val2 = (l2 != nullptr) ? l2->val : 0;

            int sum = val1 + val2 + carry;
            int digit = sum % 10;
            carry = sum / 10;

            ListNode* new_node = new ListNode(digit);
            current->next = new_node;
            current = current->next;

            l1 = (l1 != nullptr) ? l1->next : nullptr;
            l2 = (l2 != nullptr) ? l2->next : nullptr;
        }

        ListNode* result = dummy_head->next;
        delete dummy_head;

        return result;
    }
};
```

# step3

- 3 回目のコードを載せます。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy_head = new ListNode(0);
        ListNode* current = dummy_head;
        int carry = 0;

        while(l1 != nullptr || l2 != nullptr || carry != 0) {
            int val1 = (l1 != nullptr) ? l1->val : 0;
            int val2 = (l2 != nullptr) ? l2->val : 0;

            int sum = val1 + val2 + carry;
            int digit = sum % 10;
            carry = sum / 10;

            ListNode* new_node = new ListNode(digit);
            current->next = new_node;
            current = current->next;

            l1 = (l1 != nullptr) ? l1->next : nullptr;
            l2 = (l2 != nullptr) ? l2->next : nullptr;
        }

        ListNode* result = dummy_head->next;
        delete dummy_head;

        return result;
    }
};
```

# step4

- レビューのフィードバックを反映したコードを載せます。
  - ListNode
    dummy_head(0) のようにスタック領域に変数を確保することで、関数のスコープを抜けた際に自動的に解放されるように変更
  - 三項演算子をやめ、l1 = l1->next; を if 文を使用してまとめました
  - current_node の current があまり意味を持っていなかったので、node へ変更

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
  ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode dummy_head(0);
    ListNode* node = &dummy_head;
    int carry = 0;

    while(l1 != nullptr || l2 != nullptr || carry != 0) {
        int sum = 0;

        if (l1 != nullptr) {
            sum += l1->val;
            l1 = l1->next;
        }

        if (l2 != nullptr) {
            sum += l2->val;
            l2 = l2->next;
        }

        sum += carry;

        int digit = sum % 10;
        carry = sum / 10;

        ListNode* new_node = new ListNode(digit);
        node->next = new_node;
        node = node->next;
    }

    return dummy_head.next;
  }
};
```
