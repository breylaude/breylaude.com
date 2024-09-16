+++
title = "C++ Matrix Class"
description = ""
tags = [
    "c++",
]
date = "2020-06-05"
categories = [
    "compilation",
]
+++

I actually found that this code has a serious memory leak problem, but I didn’t bother to change it...

Implement overloaded equals = addition + subtraction-matrix multiplication * matrix power^ (using fast power algorithm) output «input», it will be more convenient to use, but it's a bit long.

After checking the fast power algorithm for a long time, there is no ready-made one on the Internet, so I had to push it again, and first posted the fast power algorithm that is an integer:

```c
int Pow(int x, int y){
    int ans, b, i;
    if(y == 0)return 1;
    if(y <0) return -1;
    for(i = y, b = 0; i> 0; i >>= 1) b++;
    for(ans = 1, i = b-1; i >= 0; i--){
        if(y & (1 << i)){
            ans = ans * ans * x;
        }else{
            ans = ans * ans;
        }
    }
    returnans;
}
```

Here's the code for Matrix class

```c
#include<iostream>
#include<math.h>
using namespace std;

template <typename T>
classMatrix{
//usage: Matrix<typename> varname(row, col[, mod]);
//If typename is notshort/int/long/long long
//Then the modulo operation needs to include the math.h header file (using the fmod function)
//The code for the modulo operation is only in the +-* three functions.

    public:
        T **data;
        Matrix *temp;
        introw, col, modnum;
        //No parameter constructor, when the size of the matrix is ​​not given, the output error prompt
        Matrix(){
            printf("Size parameter invalid!\n");
        }
        //Constructor, r is the number of rows and c is the number of columns. If m>0 is given, then the sum/difference/product will be modulo m
        Matrix(int r, int c, int m = 0){
            inti;
            temp =NULL;
            row = r, col = c;
            data =new T *[r]; //Assign an array of type T *[r] to data
            for(i = 0; i <r; i++){
                data[i] =newT [c]; //Allocate memory for each pointer under data, the type is T [c]
            }
            if(m> 0) modnum = m;
            elsemodnum = 0;
        }
        //Destructor function, used to release allocated memory
        ~Matrix(){
            int i;
            if(temp != NULL){delete temp;}
            for(i = 0; i <row; i++) delete data[i];
            deletedata;
        }
        //Assignment, assign a value to the element in row i and column j
        void assign(int i, int j, const T a){
            if(i <0 || i >= row || j <0 || j >= col) {
                printf("Invalid row/column number.\n");
                return;
            }else{
                data[i][j] = a;
            }
        }
        // Take out the element and give the value of the element in the i-th row and j-th column
        T at(int i, int j){
            if(i <0 || i >= row || j <0 || j >= col) {
                printf("Invalid row/column number.\n");
                return;
            }else{
                returndata[i][j];
            }
        }
        //Overload the << operator, availablecoutOutput
        friend inline ostream & operator<<(ostream &os, const Matrix &a){
            int i, j;
            for(i = 0; i <a.row; i++){
                for(j = 0; j <a.col-1; j++){
                    os << a.data[i][j] << "";
                }
                os << a.data[i][j];
                os << endl;
            }
            returnos;
        }
        //Overload>> operator, availablecinenter
        friend inline istream & operator>>(istream &is, const Matrix &a){
            int i, j;
            for(i = 0; i <a.row; i++){
                for(j = 0; j <a.col; j++){
                    is >> a.data[i][j];
                }
            }
            returnis;
        }
        //Overloaded = operator, requires two matrices to be the same size
        Matrix &operator = (const Matrix &a){
            int i, j;
            if(row != a.row || col != a.col){
                printf("Unmatch Matrix!\n");
                return*this;
            }
            for(i = 0; i <row; i++){
                for(j = 0; j <col; j++){
                    data[i][j] = a.data[i][j];
                    if(modnum> 0){
                        data[i][j] = mod(data[i][j]);
                    }
                }
            }
            return*this;
        }
        //Overloaded + operator, requires two matrices to be the same size
        Matrix &operator + (const Matrix &a){
            int i, j;
            if(row != a.row || col != a.col){
                printf("Unmatch Matrix!\n");
                return*this;
            }
            if(temp != NULL) deletetemp;
            temp =new Matrix(row, col, modnum);
            for(i = 0; i <row; i++){
                for(j = 0; j <col; j++){
                    temp->data[i][j] = data[i][j] + a.data[i][j];
                    if(modnum> 0){
                        temp->data[i][j] = mod(temp->data[i][j]);
                    }
                }
            }
            return*temp;
        }
        //Overloaded-operator, requires two matrices to be the same size
        Matrix &operator -(const Matrix &a){
            int i, j;
            if(row != a.row || col != a.col){
                printf("Unmatch Matrix!\n");
                return*this;
            }
            if(temp != NULL) deletetemp;
            temp =new Matrix(row, col, modnum);
            for(i = 0; i <row; i++){
                for(j = 0; j <col; j++){
                    temp->data[i][j] = data[i][j]-a.data[i][j];
                    if(modnum> 0){
                        temp->data[i][j] = mod(temp->data[i][j] + modnum);
                    }
                }
            }
            return*temp;
        }
        //Overload the * operator, requiring that the number of columns in matrix a is equal to the number of rows in b
        Matrix &operator * (const Matrix &a){
            inti, j, k;
            T tmp;
            if(col != a.row){
                printf("Unmatch Matrix!\n");
                return*this;
            }
            if(temp != NULL) deletetemp;
            temp =new Matrix(row, a.col, modnum);
            for(i = 0; i <row; i++){
                for(j = 0; j <a.col; j++){
                    tmp = 0;
                    for(k = 0; k <a.row; k++){
                        tmp += data[i][k] * a.data[k][j];
                        if(modnum> 0){
                            tmp = mod(tmp);
                        }
                    }
                    temp->data[i][j] = tmp;
                }
            }
            return*temp;
        }
        //Overload the ^ operator, requiring that the number of columns in the matrix is ​​equal to the number of rows
        Matrix &operator ^ (const int a){
            int i, j;
            if(row != col){
                printf("No n*n matrix!\n");
                return*this;
            }
            if(a <0){
                printf("Invalid a(%d)!\n", a);
                return*this;
            }
            if(temp != NULL) deletetemp;
            temp =new Matrix(row, col, modnum);
            for(i = 0; i <row; i++){ //unit square matrix
                for(j = 0; j <col; j++){
                    if(i == j )temp->data[i][j] = 1;
                    elsetemp->data[i][j] = 0;
                }
            }
            for(i = a, j = 0; i> 0; i >>= 1) j++;
            for(i = j-1; i >= 0; i--){
                if(a & (1 << i)){
                    *temp = *temp * *temp * (*this);
                }else{
                    *temp = *temp * *temp;
                }
            }
            return*temp;
        }
        //Overload the modulo function, yesint, long, long long, float, doubleAll work
        int mod(int i){
            returni% modnum;
        }
        long mod(long i){
            returni% modnum;
        }
        long long mod(long long i){
            returni% modnum;
        }
        float mod(float i){
            returnfmod(i, modnum);
        }
        double mod(double i){
            returnfmod(i, modnum);
        }
};

int main(){
    inti, j;
    Matrix <int> t(2, 2), t1(2, 2), t2(2, 2), a(5, 10, 7), b(10, 5), c(5, 5);
    cout << "(cin/cout test)\nPlease input 2 (2*2) Matrix: \n";
    cin >> t;
    cout<< t << endl;
    t1 = t * t * t * t * t;
    t2 = t ^ 5;
    cout<< "t^5 = \n" << t1 << endl << t2 << endl;
    getchar();
    
    for(i = 0; i <5; i++){
        for(j = 0; j <10; j++){
            a.assign(i, j, 1);
            b.assign(j, i, 2);
        }
    }
    c = a * b;
    cout << "Matrix a:" << endl << a << endl;
    cout << "Matrix b:" << endl << b << endl;
    cout<< "Matrix a * b:" << endl << c << endl;
    getchar();
    return0;
}
```
