## 单词拆分
给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词
>输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"
````js
// dfs+记忆化数组
var wordBreak = function(s, wordDict) {
   if(!s.length || !wordDict.length) return false
   let wordSet = new Set(wordDict)
   let len     = s.length
   let memo    = new Array(len)
   let dfs = function(start){
      if(start == len) return true
      if(memo[start]!==undefined) return memo[start]

      for(let i=start+1;i<=len;i++){
          let str = s.slice(start,i)
          if(wordSet.has(str) && dfs(i)){
              memo[start] = true
              return true
          }
      }
      memo[start] = false 
      return false
   }
   return dfs(0)
};
````