/*
drop table DKHP;
drop table GiangVien;
drop table HocPhan;
drop table Lop;
drop table Nganh;
drop table SinhVien;
drop table Khoa;
*/

CREATE TABLE Khoa(
	MaKhoa char(6) NOT NULL,
	TenKhoa varchar(100) NULL,
	constraint pk_khoa primary key (MaKhoa)
);

CREATE TABLE Nganh(
	MaNganh varchar(11) NOT NULL,
	TenNganh varchar(100) NULL,
	MaKhoa char(6),
	constraint pk_nganh primary key (MaNganh)
);

CREATE TABLE SinhVien(
	MaSV char(9) NOT NULL,
	TenSV char(50) NULL,
	NamSinh int NULL,
	KhoaDT varchar(6) NULL,
	MaKhoa varchar(6) NULL,
	MaNganh varchar(11) NULL,
	constraint pk_sinhvien primary key (MaSV)
);

CREATE TABLE GiangVien(
	MaGV char(6) NOT NULL,
	TenGV varchar(50) NULL,
	MaKhoa varchar(50) NULL,
	constraint pk_giangvien primary key (MaGV)
);

CREATE TABLE HocPhan(
	MaHP char(6) NOT NULL,
	TenHP varchar(100) NULL,
	HTGD varchar(6) NULL,
	SoTC int NULL,
	MaKhoa varchar(6) NULL,
	KhoaHoc varchar(6) NULL,
	constraint pk_hocphan primary key (MaHP)
);

CREATE TABLE Lop(
	MaL varchar(21) NOT NULL,
	MaGV char(6) NULL,
	MaKhoa varchar(6) NULL,
	MaHP char(6) NULL,
	Tiet char(5) NULL,
	SiSo int NULL,
	HocKi int NULL,
	NamHoc int NULL,
	constraint pk_lop primary key (MaL)
);

CREATE TABLE DKHP(
	MaSV char(9) NOT NULL,
	MaHP char(6) NOT NULL,
	MaL varchar(21) NOT NULL,
	TrangThai varchar(20) NULL,
	constraint pk_dkhp primary key (MaSV, MaHP, MaL)
);

alter table DKHP
add constraint fk_dkhp_masv
foreign key (MaSV) references SinhVien (MaSV);
alter table DKHP
add constraint fk_dkhp_mahp
foreign key (MaHP) references HocPhan (MaHP);
alter table DKHP
add constraint fk_dkhp_mal
foreign key (MaL) references Lop (MaL);

alter table GiangVien
add constraint fk_giangvien_makhoa
foreign key (MaKhoa) references Khoa (MaKhoa);

alter table HocPhan
add constraint fk_hocphan_makhoa
foreign key (MaKhoa) references Khoa (MaKhoa);

alter table Lop
add constraint fk_lop_magv
foreign key (MaGV) references GiangVien (MaGV);
alter table Lop
add constraint fk_lop_makhoa
foreign key (MaKhoa) references Khoa (MaKhoa);
alter table Lop
add constraint fk_lop_mahp
foreign key (MaHP) references HocPhan (MaHP);

alter table Nganh
add constraint fk_nganh_makhoa
foreign key (MaKhoa) references Khoa (MaKhoa);

alter table SinhVien
add constraint fk_sinhvien_makhoa
foreign key (MaKhoa) references Khoa (MaKhoa);
alter table SinhVien
add constraint fk_sinhvien_manganh
foreign key (MaNganh) references Nganh (MaNganh);

--#SET TERMINATOR @
create trigger trg_ins_sinhvien
after insert on SinhVien
referencing new as N
for each row
begin
	if N.MaNganh not in (select MaNganh from Nganh where MaKhoa = N.MaKhoa)
	then
	signal sqlstate '75000' set message_text='Loi: Ma nganh khong thuoc Khoa';
	rollback;
	end if;
end@

--#SET TERMINATOR @
create trigger trg_upd_sinhvien
after update of MaNganh, MaKhoa on SinhVien
referencing new as N
for each row
begin
	if N.MaNganh not in (select MaNganh from Nganh where MaKhoa = N.MaKhoa)
	then
	signal sqlstate '75000' set message_text='Loi: Ma nganh khong thuoc Khoa';
	rollback;
	end if;
end@

--4.
--a.
select sv.MaSV, sv.TenSV
from SinhVien sv
join DKHP dk ON sv.MaSV = dk.MaSV
where sv.MaKhoa = 'ISE'
  and dk.TrangThai != 'Huy'
group by sv.MaSV, sv.TenSV
having count(dk.MaHP) > 1;
--b.
select TenHP, TenGV, count(MaSV) as TongSoSV
from HOCPHAN HP
join DKHP DK on HP.MaHP = DK.MaHP
join LOP L on L.MaHP = DK.MaHP
join GIANGVIEN GV on GV.MaGV = L.MaGV
where TrangThai = 'Da dang ky'
group by DK.MaHP, TenHP, TenGV
having count(MaSV) > 3;
--c.
select ng.TenNganh
from DKHP dk, SinhVien sv, Nganh ng
where dk.MaSV = sv.MaSV and sv.MaNganh = ng.MaNganh
and dk.MaHP = 'IE103'
group by ng.TenNganh
order by count(*) desc
fetch first 1 rows only;
--d.
select TenGV, MaHP, count(MaL) as SoLuongLop, sum(SiSo) as TongSiSo
from Lop as l, GiangVien as gv
where l.MaGV=gv.MaGV and TenGV like '%Thu Thuy%'
group by MaHP, TenGV;
--e.
drop index idx_sinhvien_MaKhoa
drop index idx_hocphan_MaKhoa

create index idx_sinhvien_MaKhoa on SinhVien(MaKhoa);
create index idx_hocphan_MaKhoa on HocPhan(MaKhoa);

select distinct sv.MaSV, sv.TenSV
from SinhVien sv
join DKHP dkhp on sv.MaSV = dkhp.MaSV
join HocPhan hp on dkhp.MaHP = hp.MaHP
where sv.MaKhoa <> hp.MaKhoa;

--5.
--Stored procedured voi tham so vao
DROP PROCEDURE DoiTenSV;
--#SET TERMINATOR @
create procedure DoiTenSV (
    in Ma_SV varchar(10),
    in Ten_SV_Moi varchar(100)
)
language sql modifies sql data
begin
    update SinhVien
    set TENSV = Ten_SV_Moi
    where MaSV = Ma_SV;
end@
select * from SinhVien where MaSV = '23521243'
call DoiTenSV ('235212423', 'Nguyen Ki Phuong')
--Stored procedured voi tham so vao va ra
drop procedure DemSoLuongSVTheoKhoa;
--#SET TERMINATOR @
create procedure DemSoLuongSVTheoKhoa (
    in p_MaKhoa varchar(5),
    out p_SoLuongSV int
)
language sql modifies sql data
begin
    select count(*) into p_SoLuongSV
    from SinhVien
    where MaKhoa = p_MaKhoa;
    create table LogTable (
        total int);
    insert into LogTable (total) values (p_SoLuongSV);
end@
call DemSoLuongSVTheoKhoa('ISE', ?) ;
select * from LogTable;
drop table LogTable;

--Cursor
DROP TABLE KetQua_SinhVien;
CREATE TABLE KetQua_SinhVien (
    MaSV VARCHAR(10),
    TenSV VARCHAR(100)
);
--Dung cursor de duyet cac sinh vien sinh nam 2005
--#SET TERMINATOR @
CREATE OR REPLACE PROCEDURE Duyet_SinhVien_2005()
LANGUAGE SQL
MODIFIES SQL DATA
BEGIN
    DECLARE v_MaSV VARCHAR(10);
    DECLARE v_TenSV VARCHAR(100);
    DECLARE done INT DEFAULT 0;

    DECLARE cur CURSOR FOR
        SELECT MaSV, TenSV
        FROM SinhVien
        WHERE NamSinh = 2005;

    DECLARE CONTINUE HANDLER FOR NOT FOUND
        SET done = 1;

    OPEN cur;

    fetch_loop:
    LOOP
        FETCH cur INTO v_MaSV, v_TenSV;
        IF done = 1 THEN
            LEAVE fetch_loop;
        END IF;
		
		insert into KetQua_SinhVien (MaSV, TenSV)
        VALUES (v_MaSV, v_TenSV);
        
    END LOOP;

    CLOSE cur;
END
@

call Duyet_SinhVien_2005()
select * from KetQua_SinhVien

--Trigger
DROP TRIGGER GiaoVienDay5Lop;
CREATE TRIGGER GiaoVienDay5Lop
AFTER INSERT ON Lop
REFERENCING NEW AS newrow
FOR EACH ROW
WHEN (
    (SELECT COUNT(*) 
     FROM Lop 
     WHERE MaGV = newrow.MaGV 
       AND HocKi = newrow.HocKi 
       AND NamHoc = newrow.NamHoc) > 5
)
SIGNAL SQLSTATE '75000'
SET MESSAGE_TEXT = 'Mot giang vien khong the day qua 5 lop trong 1 hoc ky';

--Function
DROP FUNCTION fn_so_hp_dk;
--#SET TERMINATOR @
CREATE OR REPLACE FUNCTION fn_so_hp_dk (MASV VARCHAR(10))
RETURNS INTEGER
LANGUAGE SQL
SPECIFIC fn_so_hp_dk
BEGIN ATOMIC
    DECLARE SLHP INT DEFAULT 0;

    SET SLHP = (
        SELECT COUNT(DISTINCT MAHP)
        FROM DKHP
        WHERE MASV = fn_so_hp_dk.MASV
    );

    RETURN SLHP;
END
@
SELECT SV.MaSV, SV.TenSV, fn_so_hp_dk(SV.MaSV) AS SoHocPhan FROM SinhVien SV;

--Backup
backup database DKHP to C:\DB2_Backup;
--Restore
drop database DKHP;
restore database DKHP from C:\DB2_Backup taken at 20250427104039 replace existing;
connect to DKHP;
--Crystal Report
DROP VIEW v_so_hp_dk_moi_sv;
CREATE OR REPLACE VIEW v_so_hp_dk_moi_sv AS
SELECT 
    MASV,
    COUNT(DISTINCT MAHP) AS SO_HOC_PHAN
FROM DKHP
WHERE TRANGTHAI = 'Da dang ky'
GROUP BY MASV

--XML/XPath/XQuerry
--Tao du lieu XML tu bang
select 
  xmlserialize(
    xmldocument(
      xmlelement(name "SinhVien",
        xmlagg(
          xmlelement(name "SV",
            xmlforest(MaSV as "MaSV", TenSV as "TenSV", MaNganh as "MaNganh")
          )
        )
      )
    ) as clob(1m)
  ) as XML_SinhVien
from SINHVIEN
where MaKhoa = 'ISE';
--XPath truy van du lieu XML
--Tao bang XML
drop table xml_sinhvien_info;

create table xml_sinhvien_info (
  masv char(9),
  thongtin xml
);
--them du lieu tu bang sinhvien
insert into xml_sinhvien_info
select masv, 
       xmldocument(
           xmlelement(
               name "ThongTin",
               xmlelement(name "SinhVien",
                   xmlelement(name "MaSV", masv),
                   xmlelement(name "TenSV", tensv),
                   xmlelement(name "Nganh", manganh)
               )
           )
       )
from sinhvien
where makhoa = 'ISE';
--truy van bang xpath
select masv
from xml_sinhvien_info
where xmlcast(
         xmlquery('/ThongTin/SinhVien/Nganh' passing thongtin returning sequence)
         as varchar(20)
       ) = 'CNTT';
--xem ket qua
select
  masv,
  xmlserialize(thongtin as varchar(500)) as xml_dulieu
from xml_sinhvien_info;
--Xquerry truy van lieu XML
select xmlserialize(
         xmldocument(
           xmlelement(name "DanhSachLop",
             xmlagg(
               xmlelement(name "Lop",
                 xmlattributes(L.MaL as "MaLop", L.MaHP as "HocPhan"),
                 xmlelement(name "DanhSachSV",
                   (
                     select xmlagg(
                              xmlelement(name "SV",
                                xmlforest(S2.MaSV as "MaSV", S2.TenSV as "TenSV")
                              )
                            )
                     from DKHP D2
                     join SINHVIEN S2 on D2.MaSV = S2.MaSV
                     where D2.MaL = L.MaL and D2.TrangThai = 'Da dang ky'
                   )
                 )
               )
             )
           )
         ) as clob(1m)
       ) as XML_Lop_SV
from LOP L
where exists (
  select 1 from DKHP D where D.MaL = L.MaL and D.TrangThai = 'Da dang ky'
);

--



