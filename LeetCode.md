# LeetCode算法
## 计算char中无重复字符的最长子串长度

1. 创建一个数组list，依次遍历字符串。每次遍历到新的字符c，查看list中是否有c，如果没有，将c加入list中。如果有，移除list中c及c之前的所有字符，并且将c加入list,每次遍历设置最大长度length为max(length, list.count)。![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_1.jpg)执行结果![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_1_result.jpg)
*该方案能够解决问题，但是每次遍历涉及到数组的增删操作，效率较低*
1. 使用一个指针start记录无重复子字符串的开始位置，借助HashMap存储某个字符的最新位置，其余思想和1类似![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_2.jpg)执行结果![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_2_result.jpg)
该方法没有增删操作，一定程度上进行了优化
1. HashMap查找效率不高，结合每个字符对应一个ASCII码特性，可以使用数组创建255个元素，用字符的ASCII码作为下标，字符最新出现的位置作为value，在方案2的基础上进行优化![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_3.jpg)执行结果![](/Users/maomaoshou/Documents/notes/assets/LeetCode/char_length_3_result.jpg)