# Đố vui tháng 9
## Câu 1
 Khi thực hiện đồng thời hai lệnh transaction đó, thì có thể xảy ra trường hợp deadlock.
 Bởi vì:
  
  + Gỉa sử lệnh transaction 1 vào trước, nó sẽ chiếm giữ hàng có dữ liệu (stock_id =4 và date = '2002-05-01'), sau đó nó cần chiếm giữ hàng có dữ liệu (stock_id = 3 và date = '2002-05-02'), vì cùng 1 transaction nên lệnh update đầu tiên chưa kết thúc chừng nào lệnh update thứ hai kết thúc => hàng dữ liệu (stock_id =4) bị Transaction 1 chiếm giữ.
  + Lệnh transaction 2 vào sau, nhưng nó chiếm giữ hàng dữ liệu (stock_id = 3 và date ='2002-05-02) trước để update, sau đó có cần chiếm giữ hàng dữ liệu (stock_id = 4 và date ='2002-05-01') để thực hiện việc update. Do cùng một transaction, nên lệnh update thứ nhất chưa kết thúc chừng nào lệnh update thứ hai kết thúc => Hàng dữ liệu (stock_id =3) bị Transaction 2 chiếm giữ
  + Như vậy cả transaction 1 và 2 đều lock lẫn nhau và không ai thoát ra được => Deadlock xảy ra. 
## Câu 2
### Trình bày thuật toán:
- Cách giải thông thường : Xét tất cả các hình chữ nhật con có thể có của HCN đã cho => Cần 4 vòng lặp để có thể tính được. Độ phức tạp là O(n^4)
- Từ đó, ta sử dụng thuật toán Kadane để  giảm độ phức tạp của bài toán còn O(n^3).
- Thuật toán như sau:
  
  -Ứng với một cặp giá trị (cột left, cột right), Ta xây dựng một mảng arr, với arr[i] là tổng giá trị của dòng thứ i từ cột left đến cột right. Từ đó áp dụng thuật toán kadane để tìm ra doạn có tổng con lớn nhất trong arr. Hay nói cách khác là tìm được tổng giá trị của HCN lớn nhất trong khoảng cột left đến cột right.
 - Sau đó, giá trị Max của tất cả các cặp (left, right) chính là kết quả cần tìm.
### Source code:
~~~~
#include <iostream>
#include <fstream>
using namespace std;
int Kadane(int *arr, int &start, int &finish, int N){
    int sum=0, local_start = 0;
    int MaxSum = -2147483647-1;
    finish = -1;
    for (int i=0; i < N ; i++){
        sum += arr[i];
        if (sum < 0){
            sum=0;
            local_start = i+1;
        }
        else if (sum > MaxSum){
            MaxSum = sum;
            start = local_start;
            finish= i;
        }
    }
    if (finish != -1) return MaxSum;
    // truong hop day so deu la so am 
    MaxSum = arr[0];
    start = finish = 0;
    for (int i=1; i < N; i++)
        if (arr[i]> MaxSum){
            MaxSum= arr[i];
            start = finish = i;
        }
    return MaxSum;
}
void FindMaxSum(int ** A, int N){
    int MaxSum= -2147483647-1;
    int FinalLeft, FinalRight, FinalTop, FinalBottom, sum;
    for (int Left=0; Left < N; Left++){
        int *arr = new int[N];
        // khoi tao arr voi gia tri 0
        for (int i=0; i < N; i++) arr[i] =0;
        for (int Right= Left; Right < N; Right++){
            for (int i=0; i < N; i++) {
                arr[i] += A[i][Right];
            }
            int Top, Bottom;
             sum = Kadane(arr,Top, Bottom, N);
            if (sum > MaxSum){
                MaxSum = sum;
                FinalLeft= Left;
                FinalRight = Right;
                FinalTop = Top;
                FinalBottom= Bottom;
             }
        }
    }
    fstream outfile;
    outfile.open("output.txt", ios::out);
    outfile << MaxSum << endl;
    outfile << "Left: " << FinalLeft << endl;
    outfile << "Right: " << FinalRight << endl;
    outfile << "Top: " << FinalTop << endl;
    outfile << "Bottom: " << FinalBottom << endl;
    outfile.close();

}
int main(){
    fstream f;
    f.open("input.txt",ios::in);
    int N;
    f >> N;
    int **A = new int *[N];
    A[0] = new int[N*N];
    for (int i=1; i < N ; i++)
        A[i] = A[i-1] + N ;
    for (int i=0; i < N ; i++)
        for (int j=0; j < N; j++)
            f >> A[i][j];
    f.close();
    FindMaxSum(A,N);
    return 0;
}
~~~~
