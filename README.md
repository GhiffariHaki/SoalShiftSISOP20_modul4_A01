# SoalShiftSISOP20_modul4_A01
```
Anggota Kelompok :
Mochamad Haikal Ghiffari    -   05111840000095
Nikodemus Siahaan           -   05111840000151
```
# Enkripsi Versi 1
Semua file akan terenkripsi menggunakan Caesar Cipher dengan key :
```
9(ku@AW1[Lmvgax6q`5Y2Ry?+sF!^HKQiBXCUSe&0M.b%rI'7d)o4~VfZ*{#:}ETt$3J-zpc]lnh8,GwP_ND|jO
```
Apabila :
- Sebuah direktori dibuat dengan awalan “encv1_”, 
maka direktori tersebut akan menjadi direktori terenkripsi menggunakan metode enkripsi v1.
- Sebuah direktori di-rename dengan awalan “encv1_”, 
maka direktori tersebut akan menjadi direktori terenkripsi menggunakan metode enkripsi v1. 
- Setelah itu, apabila sebuah direktori terenkripsi di-rename menjadi tidak terenkripsi, maka isi adirektori tersebut akan terdekrip.

Oleh karena itu, kami membuat memodifikasi perintah dalam xmp_mkdir yang akan mengirim seluruh filenya kedalam fungsi enkripsi. Isi dari xmp_mkdir adalah sebagai berikut :
```
static int xmp_mkdir(const char *path, mode_t mode)
{
	char fpath[1000];
	mixPath(fpath, dirpath, path);
	
	int res;

	res = mkdir(cekPath(fpath), mode);
	if (res == -1) return -errno;
	
    char cek_substr[1024];
    if(lastPart(fpath) == 0) return 0;
    char filePath[1000000];
    strcpy(filePath, lastPart(fpath));
    substring(filePath, cek_substr, 0, 6);
	if(strcmp(cek_substr, "encv1_") == 0) //folder encrypt1
	{
		encrypt1(fpath, 1);	
	}
	writeI("MKDIR", fpath);
	return 0;
}
```
Perintah untuk memasukkannya kedalam fungsi enkripsi adalah
```
	if(strcmp(cek_substr, "encv1_") == 0) //folder encrypt1
	{
		encrypt1(fpath, 1);	
	}
```
Setelah itu kami memodifikasi xmp_rename. Dimana nantinya jika sebelumnya ada kata "encv_1" dalam 
suatu folder lalu folder tersebut di rename menjadi tidak ada kata tersebut,
maka akan dimasukkan ke dalam fungsi decrypt. Jika keadaannya terbalik, maka akan dimasukkan ke dalam fungsi encrypt.
Isi dari xmp_rename yang menandakan statement sebelumnya adalah sebagai berikut :
```
	int fromm = 0, too = 0;
	char cek_substr[1024], cek2[1024];
    if(lastPart(ffrom) == 0) return 0;
    char filePath[1000000];
    strcpy(filePath, lastPart(ffrom));
    substring(filePath, cek_substr, 0, 6);
	if(strcmp(cek_substr, "encv1_") == 0) //folder encrypt1
	{
		fromm = 1;
	}
	
    if(lastPart(fto) == 0) return 0;
    strcpy(filePath, lastPart(fto));
    substring(filePath, cek_substr, 0, 6);
	if(strcmp(cek2, "encv1_") == 0) //folder decrypt1
	{
		too = 1;
	}
	
	if(fromm == 0 && too == 1) encrypt1(fto, 1);
	else if(fromm == 1 && too != 1) encrypt1(fto, -1);
```
Dimana :
- fromm adalah adalah Nama folder sebelum rename, apabila ada encv_1 maka akan ditandai dengan 1.
- too adalah Nama folder setelah rename, apabila ada encv_1 maka akan ditandai dengan 1.

Perubahannya akan terjadi apabila 2 keadaan tersebut berlawanan dan fungsi encrypt jika parameter keduanya bernilai -1 maka akan di decrypt

KODE ENCRYPT & DECRYPT
- ENCRYPT
```
char key[90] = "9(ku@AW1[Lmvgax6q`5Y2Ry?+sF!^HKQiBXCUSe&0M.b%rI'7d)o4~VfZ*{#:}ETt$3J-zpc]lnh8,GwP_ND|jO";
int ges = 10;
char *encrypt(char* str, bool cek)
{
	int i, j, k = 0;
	char *ext = strrchr(str, '.');
	if(cek && ext != NULL) k = strlen(ext);
	for(i = 0; i < strlen(str) - k; i++)
	{
		for(j = 0; j < 87; j++)
		{
			if(str[i] == key[j]){
				str[i] = key[(j + ges) % 87];
				break;
			}
		}
	}
	return str;
}
```
- DECRYPT
```
//char key[90] = "9(ku@AW1[Lmvgax6q`5Y2Ry?+sF!^HKQiBXCUSe&0M.b%rI'7d)o4~VfZ*{#:}ETt$3J-zpc]lnh8,GwP_ND|jO";
//int ges = 10;
char *encrypt(char* str, bool cek)
{
	int i, j, k = 0;
	char *ext = strrchr(str, '.');
	if(cek && ext != NULL) k = strlen(ext);
	for(i = 0; i < strlen(str) - k; i++)
	{
		for(j = 0; j < 87; j++)
		{
			if(str[i] == key[j]){
				str[i] = key[(j + ges) % 87];
				break;
			}
		}
	}
	return str;
}
```

Setelah itu, kita harus membuat sebuah log yang mencatat apa saja yang dilakukan oleh program ini. 
Oleh karena itu kami membuat fungsi Write yang akan memberitahu informasi dan warning lalu disimpan dalam fs.log
```
void writeI(char *text, char* path)
{
    char* info = "INFO";
	char curtime[30];
    time_t t = time(NULL);
    struct tm* p1 = localtime(&t);
	strftime(curtime, 30, "%y%m%d-%H:%M:%S", p1);
    char log[1000];
    sprintf(log, "%s::%s::%s::%s", info, curtime, text, path);
	FILE *out = fopen("/home/haki/fs.log", "a");  
    fprintf(out, "%s\n", log);  
    fclose(out); 
    
}
```
Dan ada fungsi khusus untuk warning yang hanya berbeda dalam char* info.

-------------------------Selesai----------------------
