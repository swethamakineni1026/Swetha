/* Given a string formula representing a chemical formula, return the count of each atom.

The atomic element always starts with an uppercase character, then zero or more lowercase letters, representing the name.

One or more digits representing that element's count may follow if the count is greater than 1. If the count is 1, no digits will follow.

For example, "H2O" and "H2O2" are possible, but "H1O2" is impossible.
Two formulas are concatenated together to produce another formula.

For example, "H2O2He3Mg4" is also a formula.
A formula placed in parentheses, and a count (optionally added) is also a formula.

For example, "(H2O2)" and "(H2O2)3" are formulas.
Return the count of all elements as a string in the following form: the first name (in sorted order), followed by its count (if that count is more than 1), followed by the second name (in sorted order), followed by its count (if that count is more than 1), and so on.

The test cases are generated so that all the values in the output fit in a 32-bit integer.

 Example 1:

Input: formula = "H2O"
Output: "H2O"
Explanation: The count of elements are {'H': 2, 'O': 1}.*/


class Solution {
 public:
  string countOfAtoms(string formula) {
    string ans;
    int i = 0;

    for (const auto& [elem, freq] : parse(formula, i)) {
      ans += elem;
      if (freq > 1)
        ans += to_string(freq);
    }

    return ans;
  }

 private:
  map<string, int> parse(const string& s, int& i) {
    map<string, int> count;

    while (i < s.length())
      if (s[i] == '(') {
        for (const auto& [elem, freq] : parse(s, ++i))
          count[elem] += freq;
      } else if (s[i] == ')') {
        const int num = getNum(s, ++i);
        for (auto&& [_, freq] : count)
          freq *= num;
        return count;  // Return back to the previous scope.
      } else {         // `s[i]` must be uppercased.
        const string& elem = getElem(s, i);
        const int num = getNum(s, i);
        count[elem] += num;
      }

    return count;
  }

  string getElem(const string& s, int& i) {
    const int elemStart = i++;  // `s[elemStart]` is uppercased.
    while (i < s.length() && islower(s[i]))
      ++i;
    return s.substr(elemStart, i - elemStart);
  }

  int getNum(const string& s, int& i) {
    const int numStart = i;
    while (i < s.length() && isdigit(s[i]))
      ++i;
    const string& numString = s.substr(numStart, i - numStart);
    return numString.empty() ? 1 : stoi(numString);
  }
};
