title: amoeba系统性能报告
link: http://www.54chen.com/java-ee/amoeba-benchmark-report.html
author: 54chen
description: 
post_id: 1635
created: 2011/03/07 17:19:16
created_gmt: 2011/03/07 09:19:16
comment_status: open
post_name: amoeba-benchmark-report
status: publish
post_type: post

# amoeba系统性能报告

![amoeba](http://img03.taobaocdn.com/imgextra/i3/T13M02XbRXXXX4FVU4_051626.jpg) 关于amoeba，请见<http://www.54chen.com/java-ee/amoeba-rose-open-source-big-website-arch.html>，他是一个不错的mysql proxy替代工具。 **读操作** 100个并发,mysql: load:7-8

> Rate: 225 req/s Rate: 240 req/s Rate: 216 req/s Rate: 193 req/s Rate: 212 req/s Rate: 210 req/s Rate: 208 req/s Rate: 229 req/s Rate: 215 req/s Rate: 231 req/s Rate: 241 req/s Rate: 210 req/s Rate: 273 req/s Rate: 247 req/s Rate: 212 req/s

amoeba: load:13-15

> Rate: 214 req/s Rate: 185 req/s Rate: 174 req/s Rate: 181 req/s Rate: 210 req/s Rate: 194 req/s Rate: 187 req/s Rate: 190 req/s Rate: 189 req/s Rate: 167 req/s Rate: 182 req/s Rate: 182 req/s Rate: 185 req/s Rate: 178 req/s Rate: 184 req/s Rate: 187 req/s Rate: 183 req/s Rate: 196 req/s Rate: 177 req/s 

50个并发： mysql: load:5-6

> Rate: 233 req/s Rate: 207 req/s Rate: 247 req/s Rate: 231 req/s Rate: 262 req/s Rate: 195 req/s Rate: 202 req/s Rate: 224 req/s Rate: 258 req/s Rate: 256 req/s Rate: 217 req/s Rate: 183 req/s Rate: 208 req/s Rate: 265 req/s Rate: 213 req/s Rate: 244 req/s Rate: 225 req/s Rate: 238 req/s

omaeba: load:10-13

> Rate: 231 req/s Rate: 200 req/s Rate: 223 req/s Rate: 171 req/s Rate: 205 req/s Rate: 201 req/s Rate: 179 req/s Rate: 192 req/s Rate: 221 req/s Rate: 225 req/s Rate: 188 req/s Rate: 159 req/s Rate: 184 req/s Rate: 212 req/s Rate: 169 req/s Rate: 172 req/s Rate: 189 req/s Rate: 222 req/s

20个并发： mysql: load:5-6

> Rate: 208 req/s Rate: 261 req/s Rate: 257 req/s Rate: 166 req/s Rate: 218 req/s Rate: 165 req/s Rate: 240 req/s Rate: 215 req/s Rate: 218 req/s Rate: 251 req/s Rate: 197 req/s Rate: 215 req/s Rate: 242 req/s Rate: 187 req/s Rate: 196 req/s

omeba: load:6-7

> Rate: 209 req/s Rate: 160 req/s Rate: 180 req/s Rate: 152 req/s Rate: 204 req/s Rate: 192 req/s Rate: 170 req/s Rate: 214 req/s Rate: 187 req/s Rate: 217 req/s Rate: 180 req/s Rate: 225 req/s Rate: 208 req/s Rate: 198 req/s Rate: 200 req/s Rate: 211 req/s

1个并发： mysql: load:2

> Rate: 84 req/s Rate: 78 req/s Rate: 81 req/s Rate: 85 req/s Rate: 83 req/s Rate: 76 req/s Rate: 84 req/s Rate: 80 req/s Rate: 84 req/s Rate: 80 req/s Rate: 89 req/s Rate: 83 req/s Rate: 78 req/s Rate: 75 req/s

omaeba: load:3

> Rate: 80 req/s Rate: 77 req/s Rate: 79 req/s Rate: 89 req/s Rate: 73 req/s Rate: 72 req/s Rate: 73 req/s Rate: 72 req/s Rate: 82 req/s Rate: 79 req/s Rate: 67 req/s Rate: 81 req/s

**写操作** 1并发，mysql:

> Rate: 83 req/s Rate: 86 req/s Rate: 84 req/s Rate: 84 req/s Rate: 91 req/s Rate: 82 req/s Rate: 88 req/s Rate: 82 req/s Rate: 76 req/s Rate: 87 req/s Rate: 90 req/s Rate: 86 req/s Rate: 87 req/s Rate: 93 req/s Rate: 95 req/s 

1并发，amoeba: 

> Rate: 77 req/s Rate: 56 req/s Rate: 72 req/s Rate: 75 req/s Rate: 75 req/s Rate: 79 req/s Rate: 75 req/s Rate: 80 req/s Rate: 71 req/s Rate: 80 req/s Rate: 78 req/s Rate: 71 req/s

10并发，mysql: 

> Rate: 209 req/s Rate: 190 req/s Rate: 201 req/s Rate: 194 req/s Rate: 188 req/s Rate: 234 req/s Rate: 168 req/s Rate: 266 req/s Rate: 242 req/s Rate: 243 req/s Rate: 260 req/s Rate: 261 req/s Rate: 180 req/s Rate: 240 req/s Rate: 260 req/s

10并发，amoeba: 

> Rate: 132 req/s Rate: 142 req/s Rate: 154 req/s Rate: 188 req/s Rate: 164 req/s Rate: 165 req/s Rate: 188 req/s Rate: 172 req/s Rate: 173 req/s Rate: 184 req/s Rate: 147 req/s Rate: 164 req/s Rate: 172 req/s Rate: 159 req/s Rate: 133 req/s Rate: 159 req/s Rate: 191 req/s 

100并发，mysql: 

> Rate: 175 req/s Rate: 201 req/s Rate: 226 req/s Rate: 256 req/s Rate: 258 req/s Rate: 229 req/s Rate: 220 req/s Rate: 234 req/s Rate: 203 req/s Rate: 227 req/s Rate: 211 req/s Rate: 223 req/s Rate: 266 req/s Rate: 253 req/s Rate: 226 req/s Rate: 242 req/s Rate: 269 req/s Rate: 199 req/s Rate: 123 req/s 

100并发，amoeba: 

> Rate: 198 req/s Rate: 185 req/s Rate: 182 req/s Rate: 171 req/s Rate: 181 req/s Rate: 160 req/s Rate: 124 req/s Rate: 213 req/s Rate: 200 req/s Rate: 216 req/s Rate: 179 req/s Rate: 257 req/s Rate: 165 req/s Rate: 150 req/s Rate: 241 req/s

**结论** 性能还可以，网络损失和解释过程损失了一些性能，不过总体还是不错的。

## Comments

**[Timo](#13390 "2011-03-08 14:39:52"):** 跟你们以前的ice instance的方式有没有一个比较的结果

