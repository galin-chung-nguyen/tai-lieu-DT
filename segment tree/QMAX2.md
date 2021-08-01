
## QMAX2 - Giá trị lớn nhất 2

> Đề bài : https://oj.vnoi.info/problem/qmax2.
> Tóm tắt : Cho một mảng a[1 -> n] ban đầu các phần tử bằng 0.
> Có m truy vấn, mỗi truy vấn thuộc một trong 2 loại :
> - 0 l r k : yêu cầu cộng k vào đoạn a[l...r]
> - 1 l r : yêu cầu trả về giá trị lớn nhất trong đoạn a[l...r]
  

Cách giải : Xây dựng segment tree kết hợp phương pháp lazy propagation:

- Xây dựng segment tree tính max dựa vào mảng a[], mỗi nút v trên cây có 2 giá trị tương ứng : t[v] là tổng được cộng dồn vào nút v, fmax[v] là giá trị lớn nhất trong nút v tính đến hiện tại

- Cả 2 loại truy vấn được thực hiện theo phương pháp cài đặt lazy propagation thông thường (nhưng ở đây không cần hàm build vì ban đầu các giá trị trong a đều bằng 0).

  

Code:
```
uses math;

const maxN = 56789;
const oo = 123456789;

type intArr = array[1..4*MAXN] of longint;
var n,m,q,p,i,j,k,l,r : longint;
	a,t, fmax : intArr;
    
procedure push(v, tl, tr : longint);
    begin
        if(t[v] <> 0) then begin
            fmax[v] := fmax[v] + t[v]; // thực hiện thao tác cộng t[v] vào nút v, dẫn đến fmax[v] tăng lên một lượng t[v]
            if(tl < tr) then begin // đẩy giá trị lazy xuống 2 nút con
                t[v * 2] := t[v * 2] + t[v];
                t[v * 2 + 1] := t[v * 2 + 1] + t[v];
            end;
            t[v] := 0;
        end;
    
    end;

procedure update(v, tl, tr, l, r, x : longint); // cộng x vào đoạn [l,r]
    var tmid : longint;
    begin
        push(v,tl,tr); // thực hiện các truy vấn trước đó được dồn lại tại nút v (nếu có)
        if (l > tr) or (tl > r) then exit; // nếu 2 đoạn không giao nhau thì thoát
        if (l <= tl) and (tr <= r) then begin // nếu [tl,tr] nằm trong [l,r]
            t[v] := t[v] + x;
            push(v, tl, tr); // lại push tiếp tại nút v
        end else begin
            tmid := (tl + tr) div 2;
            update(v * 2, tl, tmid, l, r, x); // 
            update(v * 2 + 1, tmid + 1, tr, l, r, x); //
            fmax[v] := max(fmax[v * 2],fmax[v * 2 + 1]); // cập nhật fmax[v] sau khi đã cập nhật hai nút con
        end;
    end;

function getMax(v, tl, tr, l, r : longint): longint; // tìm giá trị lớn nhất trong phần giao của [tl,tr] và [l,r]
    var tmid : longint;
    begin
        push(v,tl,tr); // thực hiện các truy vấn trước đó được dồn lại tại nút v (nếu có)
        if (l > tr) or (tl > r) then exit(0); // 2 đoạn không giao nhau
        if (l <= tl) and (tr <= r) then exit(fmax[v]); // nếu [tl,tr] nằm trong [l,r]
        tmid := (tl + tr) div 2;
        exit(max(getMax(v * 2, tl, tmid, l, r), getMax(v * 2 + 1,tmid + 1, tr, l, r))); // lấy max cả 2 trường hợp
    end;
    
begin
    readln(n,m);
    
    for i:=1 to n do a[i] := 0;
    
    //build(a, 1, 1, n);
    
    for i := 1 to m do begin
        read(p,l,r);
        if(p = 0) then begin
            readln(k);
            update(1,1,n,l,r,k); // cộng k vào đoạn [l,r]
        end else begin
            readln;
            writeln(getMax(1,1,n,l,r)); // lấy max của đoạn [l,r]
        end;
    end;
end.
```