---
layout: post
category: C++
title: C++ universal/uniform/brace initialization
tagline: by SunHaozhe
tags: 
  - C++
  - API
mathjax: true
comments: true
published: true
---


Universal/uniform/brace initialization introduced in C++11

```c++
vector<vector<int>> res;
res.push_back({0, 1});
// res[0] is a vector
```

```c++
vector<vector<int>> res;
res.push_back(vector<int>{0, 1});
// res[0] is a vector
```

```c++
vector<list<int>> res;
res.push_back(list<int>{0, 1});
// res[0] is a list
```

```c++
struct Student{
    int id, grade;
};

int main(){
    Student s{1, 68};
    
    vector<Student> v;
    v.push_back(Student{2, 90});
    v.push_back({3, 56});
    cout << v.size() << endl; // 2
    for(auto e: v) cout << e.id << ", "; cout << endl; // 2, 3,
}
```


One trap of universal initializations is shown here:

```c++
vector<int> v1(4, 100); // [100, 100, 100, 100], fill constructor
vector<int> v2{4, 100}; // [4, 100], equivalent to vector<int> v = {4, 100};

vector<vector<int>> vv;
vv.push_back(vector<int>(4, 100));
vv.push_back(vector<int>{4, 100});
for(auto e: vv[0]) cout << e << ", "; cout << endl; // 100, 100, 100, 100, 
for(auto e: vv[1]) cout << e << ", "; cout << endl; // 4, 100, 

```


