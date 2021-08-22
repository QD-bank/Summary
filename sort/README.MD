# 数组排序

给出正整数数组 array = [2,1,5,3,8,4,9,5]
请写出一个函数 sort，使得 sort(array) 得到从小到大排好序的数组 [1,2,3,4,5,5,8,9]

计数排序：
```javascript
let countingSort = arr =>{
    arr = [2,1,5,3,8,4,9,5]
          //空的哈希表、最大值、返回结果
          let hashTable = {}, max = 0, result = []

          //遍历数组
          for(let i = 0; i < arr.length; i++){
              //判断i是否在哈希表里面
              if(!(arr[i] in hashTable)){
                  hashTable[arr[i]] = 1
              }else{
                  hashTable[arr[i]] += 1
              }

              //谁比我大我就等于谁
              if(arr[i] > max){
                  max = arr[i]
              }
          }
           //遍历哈希表
           for(let j = 0; j <= max; j++){
                  if(j in hashTable){
                      for(let i = 0; i < hashTable[j]; i++){
                          result.push(j)
                      }
                  }
              }
          return result
      }
```