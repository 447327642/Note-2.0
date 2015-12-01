# Amazon OA

发一个Amazon oa2 的面筋，work simulation的部分时间非常充足，可以慢慢做，就是问你要是你你会怎么选，你同意谁的观点和给一下几个做法打分的题，有一个debug的题就是OOD的题，一共三道，第一个是问你其中有一个方法有什么问题，第二个是问有一个shopping cart class 有什么问题，第三个是给了五个test case问能不能过，记得有一个是user类，构造函数有四个参数但是函数里只给其中三个赋值了email没有赋值，有一个test case大概是User user = new User("abc","age","address","abc@gmail.com") 问email最后等不等于abc@gmail.com我选的错。看log的也不难就是找相同出错原因问那几条的相同点就可以。code test是round robin和rotate matrix， round robin补充一点我的题里写的是可以假设只要有任务开始以后cpu是不会空闲的，也就是说cpu开始后如果空闲了就说明没有任务了。

---

一进online test就是马鬃家的各种介绍video，心想camera怎么没开，难道浏览器坏了？后来要开始70分钟code才开始拍照。要是早知道就录像了。唉~~
前面work simulation很好玩，就是各个员工讨论case media network 服务器最近好多complaints,有德国的，有invalid recommendation的，给了个列表好多国家的服务器返回什么404/ german recommendation/ invalid recom/问是什么原因。还有俩个年轻老白讨论客人要强烈要求有硬皮书的推荐，但服务器里只有digital版本的，到底要不要加这个功能，感觉后面的视频是根据你的选择来的（有待考证）；里面有个会议室白人，亚裔，烙印在讨论服务器最近好多complaints,然后我选则的要看Intenal test，结果后面会议结束烙印站起来义正言辞跟我说，我已经写了20年服务器了，不可能有错误的，而且我刚刚才调试过机器，绝对不可能是内部错误。呵呵，里面有个选项问，烙印 is not helpful...只能呵呵~~ 大部分跟地里说的一样，类似问卷调查，选deadline更重要 和用户体验更重要。
OA 2是翻转列表(signature 是LNode list) 和SJF(返回float). 调了好久，case总算都通过了，原来翻转列表如果mid在正中间，从mid开始翻，不是正中间，从mid+1开始翻，地里这个题目也没来得及挖，不知道是不是大家都知道了。SJF的是参考地里的，有谁有具体的题目啊？考试也没来的及看。谢谢。另外附上我写的find path in maze的代码，BFS的，地里好像没有, 试了几个case,好像都对，这里没有写四个方向的循环，用了数组来代替，记得有面试Microsoft的问，走迷宫不想写四个方向的重复，怎么弄，应该就是用方向的数组来代替。另外想要题目code的可以给我邮件，谢谢！ 
Happy Thanksgiving!. 鍥磋鎴戜滑@1point 3 acres

public class findMazepath {
      private static boolean bfs(int[][] maze, int startx, int starty) {
            // TODO Auto-generated method stub
            if(maze == null)
                  return false;
            if(maze.length == 0 || maze[0].length == 0)
                  return false;
            LinkedList<Node>queue = new LinkedList<Node>();
            int[][] Direction = {{-1,0}, {0, -1}, {1, 0}, {0, 1}}; //方向：左上右下     
            Noden1 = new Node(0, 0, maze[0][0]);
            queue.offer(n1);
      
            int M = maze.length;
            int N = maze[0].length;
            
            while (!queue.isEmpty()) {
                  Nodenode = queue.poll();
                  if (node.val == 9) {
                        return true;
                  }
                  for(int i = 0; i < 4; i++){
                        int x = node.x + Direction[i][0];
                        int y = node.y + Direction[i][1];
                        //bfs
                        if(x >= 0 && x < M && y >= 0 && y < N && maze[x][y] > 0){
                              NodenewNode = new Node(x, y, maze[x][y]);
                              queue.offer(newNode);
                              maze[x][y] = -1;
                        }
                  }
            }
            return false;
      }
      
      
  ---
  
  早上早做完OA1， 基本跟地里的面经一样debug题有一道for忘记大括号， 一个sort的大小写符号反了， 剩下的记不大住了。。。

. from: 1point3acres.com/bbs 
reasoning的题考的是五男三女问哪些可能的组合方式
还有就是给一堆条件问是否应该给员工分房
还有印度广告公司问题

Coding题是window sum
LZ用的C++， 本来在地里听说signature给的是指针， 结果发现给的都是std库里的。 可以随便用vector啥的 很方便。

---

degub部分地里基本都有。
insert sort descending order: <>反了
selection sort: arr[min]>arr[x] 改成arr[y]. visit 1point3acres.com for more.
reverse array: arr[len-1]改成arr[len-i-1], 循环结束前去掉len+=1;. 鐗涗汉浜戦泦,涓€浜╀笁鍒嗗湴
Print Pattern: for loop 加括号. 涓€浜�-涓夊垎-鍦帮紝鐙鍙戝竷
11
1111
111111.鐣欏璁哄潧-涓€浜�-涓夊垎鍦�

其他的记不起来了。

reasoning 部分
时间还是比较紧的，中间有几道题基本都是蒙的，后面的题有那个印度公司，bike shop，charge delivery fee, MBA, 六人圆桌问题。

coding
window sum.

---

刚做完Amazon OA2 来更新一下。
Coding 是 MissCache 和 Insert into cycle linkelist

---

亚麻OA2 11/30duework simulation全是面筋原题，没变动过
coding是，min path sum，跟 shorest job first

---

OA1遇到的debug和logic问题地里面经基本全都概括了。logic时间略紧，不过看过面经再做问题不大。coding题是rectangle overlap。
. Waral 鍗氬鏈夋洿澶氭枃绔�,
OA2 work simulation部分我觉得时间没传说的那么宽裕，可能因为有些题纠结太长时间。coding是linkedlistinsert和subtree。

---

OA2:
LRU miss count
Life game
10月做的OA2，等了3周。
整个OA...一个半小时做完的...前面的题毕竟没认真做
Video:
讲讲算法思路，时间复杂度。比如LRU，因为没有用hash，所以complexity是N^2. 1p

---

Debug:
两题是for loop 加上大括号
一题排序 < > 反了
一题循环里要加上 i++， 否则死循环
其他记不起来了，都挺简单. more info on 1point3acres.com
. 1point3acres.com/bbs
Reasoning:
字母找顺序全部是+4，+2之类的，或者奇数+4 偶数-4, 提前列好字母和数字的对应关系。
有一题是0, 1, 1, 2，3， 7，（22）
其他找规律好像挺简单的。
印度公司，广告那题。
四个人，咖啡机，卫生间那题有4问。
region code < 10, > 10 那题。
上面提到的地里有详细的OA整理。

Coding:
Rectangle Overlap。这题和leetcode 算相交面积的区别：它帮你定义好两个类，一个叫Point，一个叫Rectangle，Rectangle包括左上右下两个Point, Point包括x, y的值， 这种细节并不影响程序，总之一句判断直接通过了全部20多个case.

---

Coding的话 我碰到的两道是 Linkedlist insert 和 LRU cache miss
之前面经有很多了 算法我就不说了由于我是用c＋＋写的 看到地里很少有c＋＋的帖子 所以这里补充一些东西：
.鐣欏璁哄潧-涓€浜�-涓夊垎鍦�
Linkedlist里面 给的 cnode 只有default的constructor 必须new完之后再赋值
LRU cache miss 给的三个input是 int max_cache_size int* pages 和 int len。 在写的时候我刚开始用find来找有没有cache hit 结果编译之后发现过不了 手动include 了 algorithm 也不行，最后没找出来什么原因就改成了 for loop.

---

Coding部分没什么好说的，matrix rotate 和 round robin.
round robin出现奇怪的bug一直没调过去，video已经无望求onsite.

重点说说simulation. 第一个情境是给图书馆写图书推荐系统，第一问让两个人继续说，第二问选图书馆的服务器有没有开放关于实体书的api
-google 1point3acres
后面有会议说系统出现bug，该做出什么反应，选看internal bug 记录。
. From 1point 3acres bbs
最后是五个case看哪个可以通过，前人都提示过，注意user的构造函数没有给email赋值。

---

给一个CPoint数组，求离原点最近的k个点。. 鍥磋鎴戜滑@1point 3 acres
class CPoint{
      double x;
      double y;
}
. from: 1point3acres.com/bbs 
我就按照离原点距离 x*x+y*y 排了个序，取前k个。 test cases 都过了。

---

刚做完OA2，来回报一下地里OA1 coding：windowSum(20号做完， 21收到OA2)

OA2 work simulation就是看email，chat...大家记得每收到email就要看看，我当时碰到没有题的email直接跳过，后来做题的时候做了几道发现信息很少做不出来随便乱选了，翻了翻记录才发现有些信息都在那些没题的email里了。。看log得题就找相同错误的规律，我记得有道我选了地点都在德国，有个是因为username太长没存全， testcase就是地里说的那些email没有初始化， 返回值类型还有一个好像返回了空，为了方便做题，我当时把5个testcase照了下来，需要得可以留邮箱
OA2 coding：insert node in circular list, LRU miss count
OA2第二题用了十分钟写出来，几乎没怎么调就过了（发现unordered_map用不了改用map就好了），第一题也是用了10mins写出来，但是不知道为什么，用c++做得时候一直runtime error, 但给的两个testcase都对了，开始以为程序有问题， 看了好久， 后来发现只要用c++并且返回了一个new出来的pointer，就开始runtime error. 调了很久无果，中途都开始翻网页了但是没看见有警告啥的，不知道这算不算违规，最后10mins转写JAVA， 其实就是直接把代码粘过去然后调了一下语法，就过了所有的testcases...所以Amazon对C++是多不友好啊。。也不知道是不是真的因为C++的pointer比较麻烦所以出错了还是他们系统有问题，最后真的简化到注释了所有code, 只new了一个pointer再返回，结果还是runrime error, 建议能用JAVA的就不要用C++了。。听说给不给video跟做题的时间和debug次数还有关。。真是欲哭无泪啊。。。

---

OA2 work simulation 比我想象的要逼真的多， 正如地理描述的有模拟会议， 接收邮件，chat 等等。找bug in error log之类的， 我基本都凭着感觉选的，真不知道亚麻看的是什么标准

Coding 我拿到的是： reverse second half of linked list 和 round robin. . From 1point 3acres bbs
第一题 我刚开始忘记check corner case结果hidden test cases 都没过 然后发现了问题， 全过
round robin： 代码很快写完了 然后发现我class constructor 里面有typo wtf..haha counldn't compile. fixed it。然后default test case都没过， 慌了一下然后发现 我在curtime 负值时写错了 最后都过了。两题一共花了大概22分钟吧

