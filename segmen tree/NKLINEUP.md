
## NKLINEUP - Xếp hàng

> Đề bài : https://oj.vnoi.info/problem/nklineup
> Tóm tắt : Cho một mảng a[1 -> n].
> Có q truy vấn, mỗi truy vấn có dạng [l,r] yêu cầu tính chênh lệch của phần tử lớn nhất và phần nhỏ nhỏ nhất trong đoạn này (tức là max(a[l...r]) - min(a[l...r])).

Cách giải : Xây dựng segment tree có 2 mảng tmax[], tmin[]:
- tmax dùng để lưu giá trị lớn nhất của mỗi nút, tmin dùng để lưu giá trị nhỏ nhất của mỗi nút
- Xây dựng segment tree tính tmax,tmin dựa vào mảng a[]
- Với mỗi truy vấn [l,r], sử dụng 2 hàm getMax và getMin để tính max/min trong đoạn này và trả về kết quả.

 Code:
```
uses math;

const maxN = 56789;
const oo = 123456789;

type intArr = array[1..4*MAXN] of longint;
var n,q,i,k,l,r : longint;
	a,tmax,tmin : intArr;

procedure build(var a : intArr; v,tl,tr : longint);
	var tmid : longint;
	begin
		if(tl = tr) then begin
		    tmax[v] := a[tl];
		    tmin[v] := a[tl];
		end else begin
			tmid := (tl + tr) div 2;
			build(a,v * 2, tl,tmid); // gọi đệ quy tới 2 nút con
			build(a,v * 2 + 1,tmid + 1,tr); // 
			tmax[v] := max(tmax[v * 2],tmax[v * 2 + 1]); // sau đó lấy max/min rồi gán cho nút hiện tại
			tmin[v] := min(tmin[v * 2],tmin[v * 2 + 1]);
		end;
	end;
	
function getMax(v, tl, tr, l, r : longint):longint; // lấy giá trị max trong phần giao của [tl,tr] và [l,r]
	var tmid : longint;
	begin
		if (l > r) or (l > tr) or (tl > r) then exit(0); // nếu [tl,tr] và [l,r] không giao nhau thì thoát
		if (l <= tl) and (tr <= r) then exit(tmax[v]); // nếu [tl,tr] nằm hoàn toàn trong [l,r] thì lấy nút này
		tmid := (tl + tr) div 2;
		exit(max(getMax(v * 2, tl, tmid, l, r) , getMax(v * 2 + 1, tmid + 1, tr, l, r))); // gọi truy vấn tới 2 nút con rồi lấy max
	end;
	
function getMin(v, tl, tr, l, r : longint):longint; // lấy giá trị min trong phần giao của [tl,tr] và [l,r]
	var tmid : longint;
	begin
		if (l > r) or (l > tr) or (tl > r) then exit(oo); // nếu [tl,tr] và [l,r] không giao nhau thì thoát
		if (l <= tl) and (tr <= r) then exit(tmin[v]); // nếu [tl,tr] nằm hoàn toàn trong [l,r] thì lấy nút này
		tmid := (tl + tr) div 2;
		exit(min(getMin(v * 2, tl, tmid, l, r) , getMin(v * 2 + 1, tmid + 1, tr, l, r))); // gọi truy vấn tới 2 nút con rồi lấy max
	end;
begin
    readln(n,q);
    
    for i := 1 to n do readln(a[i]);
    
    build(a,1,1,n); // xây dựng segment tree
    
    for i := 1 to q do begin
        readln(l,r);
        writeln(getMax(1,1,n,l,r) - getMin(1,1,n,l,r));
    end;
end.
```