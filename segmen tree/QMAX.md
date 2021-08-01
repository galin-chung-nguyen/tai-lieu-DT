## QMAX - Giá trị lớn nhất
> Đề bài : https://oj.vnoi.info/problem/qmax
> Tóm tắt : Cho một mảng a[1 -> n] ban đầu các phần tử bằng 0.
> Có m truy vấn, mỗi truy vấn có dạng [u,v,k] yêu cầu cộng k vào đoạn a[u...v]
> Sau khi thực hiện m truy vấn này, có p truy vấn có dạng [u,v] yêu cầu lấy max trong đoạn a[u...v].

1. Để xử lí m truy vấn đầu tiên, ta sử dụng phương pháp mảng cộng dồn : với mỗi truy vấn [u,v,k], cộng k vào a[u] và cộng -k vào a[v + 1]. Sau đó duyệt từ a[1] -> a[n], cộng vào a[i] giá trị của a[i - 1], như vậy ta sẽ thu được giá trị của a[i].
2. Để xử lí p truy vấn tiếp theo, ta sử dụng segment tree như sau:
	- Xây dựng segment tree tính max dựa vào mảng a[]
	- Với mỗi truy vấn [u,v], truy vấn trên segment tree giá trị lớn nhất trong đoạn [u,v]
	- Như vậy, trong bài toán này chỉ cần cài đặt 2 hàm cho segment tree: build và getMax. 

Code:

```
uses math;

const maxN = 56789;

type intArr = array[1..4*MAXN] of longint;
var n,m,p,i,u,v,k,l,r : longint;
	a,t : intArr;

procedure build(var a : intArr; v,tl,tr : longint);
	var tmid : longint;
	begin
		if(tl = tr) then t[v] := a[tl]
		else begin
			tmid := (tl + tr) div 2;
			build(a,v * 2, tl,tmid); // gọi đệ quy tới 2 nút con
			build(a,v * 2 + 1,tmid + 1,tr); // 
			t[v] := max(t[v * 2],t[v * 2 + 1]); // sau đó lấy max rồi gán cho nút hiện tại
		end;
	end;
	
function getMax(v, tl, tr, l, r : longint):longint; // lấy giá trị max trong phần giao của [tl,tr] và [l,r]
	var tmid : longint;
	begin
		if (l > r) or (l > tr) or (tl > r) then exit(0); // nếu [tl,tr] và [l,r] không giao nhau thì thoát
		if (l <= tl) and (tr <= r) then exit(t[v]); // nếu [tl,tr] nằm hoàn toàn trong [l,r] thì lấy nút này
		tmid := (tl + tr) div 2;
		exit(max(getMax(v * 2, tl, tmid, l, r) , getMax(v * 2 + 1, tmid + 1, tr, l, r))); // gọi truy vấn tới 2 nút con rồi lấy max
	end;
begin
    readln(n,m);
    
    for i:= 0 to n do a[i] := 0;
    
    for i := 1 to m do begin
        readln(u,v,k);
        a[u] := a[u] + k; // cộng vào a bằng phương pháp mảng cộng dồn (tổng tiền tố)
        a[v + 1] := a[v + 1] - k; //
    end;
    
    for i:= 1 to n do a[i] := a[i] + a[i - 1]; // cập nhật giá trị cho mảng a
    
    build(a,1,1,n); // xây dựng segment tree
        
    readln(p);
    for i := 1 to p do begin
        readln(l,r);
        writeln(getMax(1,1,n, l,r)); // lấy max trong đoạn [l,r]
    end;
end.
```