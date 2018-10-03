# Session

## Sticky Session / Cookie stickiness

當服務有超過 1 台以上的主機時，在主機前面會使用 Load balancer 去做 Request 的分流，假設有 3 台主機，同一個使用者的 Session 就會分散在不同的主機上，會導致使用者在不同主機的 Session 資料不一樣，可能使用者在主機 1 登入狀態，在主機 2 卻是登出的狀態。

可以使用 Sticky Session，讓同一個使用者的 Request 都指向固定一台實體主機，這樣可保持使用者登入的狀態


## 參考資料
* [Sticky and NON-Sticky sessions - Stack Overflow](https://stackoverflow.com/questions/10494431/sticky-and-non-sticky-sessions)
* [New Elastic Load Balancing Feature: Sticky Sessions | AWS News Blog](https://aws.amazon.com/blogs/aws/new-elastic-load-balancing-feature-sticky-sessions/)
