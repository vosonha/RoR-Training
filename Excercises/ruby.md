# Synthetic Exercise

Viết 1 class tên là `Person` và dùng class này cho những bài tập sau:

1. Mỗi `Person` có 2 instance variable là `name` và `age`. Tạo setter và getter cho `name` và `age`

2. Khi khởi tạo 1 `Person` có thể truyền `name` và `age`

3. Class `Person` có 1 class variable là count.

4. Mỗi lần khởi tạo 1 `Person` thì biến count sẽ tăng lên 1.

5. Person có 1 instane method tên là `introduce` sẽ in ra kết quả là: "My name is <name của person đó>. I`m <age của person đó> years old".

6. Person có 1 class method tên là `total_count` sẽ in ra kết quả là: "Total number of people is <tổng số Person đã được tạo>"

7. Dùng hàm `times` tạo 1 mảng `people` gồm 20 person với name là `Person 1` cho đến `Person 20`, age của mỗi `Person` random từ 10 -> 90

8. Với mảng `people` lọc ra những `Person` có tuổi nhỏ hơn 18

9. Với mảng `people` xóa những `Person` có tuổi nhỏ hơn 18

10. Sort mảng `people` theo tuổi tăng dần.

11. Sort mảng `people` theo tuổi giảm dần.

12. Delete 1 phần tử ở vị trí xác định trong mảng `people`

13. Xem phần module [Enumerable của Ruby](https://ruby-doc.org/core-2.5.1/Enumerable.html)(Array và Hash trong Ruby có include module này) để tìm ra **Person lớn tuổi nhất**, **Person nhỏ tuổi nhất**.

14. Dùng **CÁC** vòng lặp trong Ruby và **CÁC** hàm (khả thi) của `Array` để **tăng tuổi** của từng `Person` trong mảng `people` lên 1.

15. Không dùng vòng lặp (dùng **CÁC** hàm của Array) lấy ra tất cả các `age` của các Person trong mảng `person`

16. Tạo 1 mảng `people_2` tương tự câu 7, nối mảng `people_2` vào `people`

17. Viết 1 instance method bất kì, raise Exception (với message bất kì) trong method đó, sau đó catch exception và in ra message của Exception đó.

