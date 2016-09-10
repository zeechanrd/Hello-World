# Hello-World
Test for Git
#define BookSize 100
#define BLHnum 50
#define RRnum 50
#include<stdio.h>
#include<string.h>
#include "type.cpp"
int b1=1;


int BinSearch(BnoIdxFile bif,int key)
{ 
	int low,high,mid;
	low=1;
	high=bif.len;
	while(low<=high) {
		mid=(low+high)/2;
		if(key==bif.BnoIdx[mid].bno)
			return bif.BnoIdx[mid].RecNo;
		else if(key<bif.BnoIdx[mid].bno)
			high=mid-1;
		else low=mid+1;
	}
	return 0;
}

void BorrowBook(BookDbaseFile *bf,BnoIdxFile bif,BbookFile *bbf,ReadFile *rf)
{
	char jyrq[9];
	int sh,dzh;
	int i,j,k=0;
	printf("输入读者号  书号 借阅日期\n");
	scanf("%d%d%s",&dzh,&sh,jyrq);
	for(i=1;i<=rf->len;i++)
		if(dzh==rf->ReadRec[i].rno)
		{
			k=i;break;
		}
		if(k==0) 
		{ 
			printf("非法读者! \n");
			return;
		}
		if(rf->ReadRec[k].bn2>=rf->ReadRec[k].bn1)
		{ 
			printf("书已借满! \n");
			return;
		}
		j=BinSearch(bif,sh);
		if(j==0) 
		{ 
			printf("非法书号! \n");
			return;
		}
		if(bf->BookDbase[j].borrownum>=bf->BookDbase[j].storenum)
		{ printf("图书已借出\n");return;}
		i=++bbf->len;
		bbf->Bbook[i].rno=dzh;
		bbf->Bbook[i].bno=sh;
		strcpy(bbf->Bbook[i].date1,jyrq);
		rf->ReadRec[k].bn2++;
		bf->BookDbase[j].borrownum++;
		printf("借书成功! \n");
}

void BackBook(BookDbaseFile *bf,BnoIdxFile bif,BbookFile *bbf,ReadFile *rf)
{ 
	char hsrq[8];
	int sh,dzh;
	int i,j,k=0,m=0;
	printf("读者号  书号  还书日期\n");
	scanf("%d%d%s",&dzh,&sh,hsrq);
	for(i=1;i<=rf->len;i++)
		if(dzh==rf->ReadRec[i].rno)
		{ k=i;break;}
		if(k==0) { printf("非法读者! \n");return;}
		for(i=1;i<=bbf->len;i++)
			if(sh==bbf->Bbook[i].bno)
			{ 
				m=i;
				break;
			}
			if(m==0) { 
				printf("非法书号! \n");
				return;
			}
			j=BinSearch(bif,sh);

			if(j==0) 
			{ 
				printf("非法书号! \n");
				return;
			}
			rf->ReadRec[k].bn2--;
			bf->BookDbase[j].borrownum--;
			strcpy(bbf->Bbook[m].date2,hsrq);
			printf("还书成功! \n");
}

BookDbaseFile AppeDBaseRec(BookDbaseFile df)
{ 
	int i;
	i=++df.len;
	printf("书号  书  名  作者名  出版社  分类  藏书量\n");
	scanf("%d%s",&df.BookDbase[i].bno,df.BookDbase[i].bname);
	scanf("%s%s",df.BookDbase[i].author,df.BookDbase[i].press);
	scanf("%s%d",df.BookDbase[i].sortno,&df.BookDbase[i].storenum);
	df.BookDbase[i].borrownum=0;
	return df;
}

BnoIdxFile ChangeBnoIdxF(BookDbaseFile df,BnoIdxFile bif)
{ 
	int i,j,k=1;
	i=df.len;
	j=bif.len;
	while(j>=1)
	{
		if(df.BookDbase[i].bno>bif.BnoIdx[j].bno)  
		{ 
			k=j+1;
			break;
		}
		j--;
	}
	if(bif.len>0)
		for(j=bif.len;j>=k;j--)
			bif.BnoIdx[j+1]=bif.BnoIdx[j];
		bif.BnoIdx[k].bno=df.BookDbase[i].bno;
		bif.BnoIdx[k].RecNo=i;
		bif.len++;
		return bif;
}

LHFile1 ChangeLinkHeadF1(BookDbaseFile *df,LHFile1 lhf1)
{
	int i,j,k,m;
	char sm[21];
	i=df->len;
	strcpy(sm,df->BookDbase[i].bname);
	j=1;k=0;
	while(j<=lhf1.len1)
	{
		if(strcmp(sm,lhf1.LHFrec1[j].bname)==0)
		{ 
			k=j;
			break;
		}
		j++;
	}
	if(k!=0)
	{
		df->BookDbase[i].namenext=lhf1.LHFrec1[k].lhead;
		lhf1.LHFrec1[k].lhead=i;
		lhf1.LHFrec1[k].RecNum++;
	}
	else
	{
		m=++lhf1.len1;
		df->BookDbase[i].namenext=0;
		lhf1.LHFrec1[m].lhead=i;
		lhf1.LHFrec1[m].RecNum=1;
		strcpy(lhf1.LHFrec1[m].bname,sm);
	}
	return lhf1;
}

LHFile2 ChangeLinkHeadF2(BookDbaseFile *df,LHFile2 lhf2)
{ 
	int i,j,k,m;
	char zz[9];
	i=df->len;
	strcpy(zz,df->BookDbase[i].author);
	j=1;k=0;
	while(j<=lhf2.len2)
	{
		if(strcpy(zz,lhf2.LHFrec2[j].author)==0)
		{ 
			k=j;
			break;
		}
		j++;
	}
	if(k!=0) 
	{
		df->BookDbase[i].authnext=lhf2.LHFrec2[k].lhead;
		lhf2.LHFrec2[k].lhead=i;
		lhf2.LHFrec2[k].RecNum++;
	}
	else 
	{ 
		m=++lhf2.len2;
		df->BookDbase[i].authnext=0;
		lhf2.LHFrec2[m].lhead=i;
		lhf2.LHFrec2[m].RecNum=1;
		strcpy(lhf2.LHFrec2[m].author,zz);
	}
	return lhf2;
}

LHFile3 ChangeLinkHeadF3(BookDbaseFile *df, LHFile3 lhf3)
{ 
	int i,j,k,m;
	char cbs[11];
	i=df->len;
	strcpy(cbs,df->BookDbase[i].press);
	j=1;k=0;
	while(j<=lhf3.len3)
	{
		if(strcmp(cbs,lhf3.LHFrec3[j].press)==0)
		{
			k=j;
			break;
		}
		j++;
	}
	if(k!=0)
	{
		df->BookDbase[i].prenext=lhf3.LHFrec3[k].lhead;
		lhf3.LHFrec3[k].lhead=i;
		lhf3.LHFrec3[k].RecNum++;
	}
	else 
	{ 
		m=++lhf3.len3;
		df->BookDbase[i].prenext=0;
		lhf3.LHFrec3[m].lhead=i;
		lhf3.LHFrec3[m].RecNum=1;
		strcpy(lhf3.LHFrec3[m].press,cbs);
	}
	return lhf3;
}

ReadFile ReaderMange(ReadFile rf)
{
	int i;
	char yn='y';
	i=++rf.len;
	while(yn=='y'||yn=='Y')
	{
		printf("输入读者号   读者名   可借图书数\n");
		scanf("%d%s",&rf.ReadRec[i].rno,rf.ReadRec[i].name);
		scanf("%d",&rf.ReadRec[i].bn1);
		rf.ReadRec[i].bn2=0;
		printf("继续输入吗? y/n:    ");
		getchar();
		scanf("%c",&yn);i++;
	}
	rf.len=i-1;
	return rf;
}

void readfile(BookDbaseFile *bf,BnoIdxFile *bif,LHFile1 *f1,LHFile2 *f2,LHFile3 *f3,ReadFile *rf,BbookFile *bbf)
{ 
	FILE *fp;int i;
	fp=fopen("book","rb");
	i=1;
	while(!feof(fp)) 
	{
		fread(&bf->BookDbase[i],sizeof(BookRecType),1,fp);
		i++;if(feof(fp))break;
	}
	bf->len=i-2;fclose(fp);
	fp=fopen("bidx","rb");
	i=1;
	while(!feof(fp))
	{
		fread(&bif->BnoIdx[i],sizeof(BidxRecType),1,fp);
		i++;
	}
	
	bif->len=i-2;fclose(fp);
	fp=fopen("nidx","rb");
	i=1;
	while(!feof(fp))
	{
		fread(&f1->LHFrec1[i],sizeof(BNRecType),1,fp);
		i++;
	}
	
	
	f1->len1=i-2;fclose(fp);
	fp=fopen("aidx","rb");
	i=1;
	while(!feof(fp)) 
	{
		fread(&f2->LHFrec2[i],sizeof(BARecType),1,fp);
		i++;
	}
	
	f2->len2=i-2;
	fclose(fp);
	fp=fopen("pidx","rb");
	i=1;
	while(!feof(fp)) {
		fread(&f3->LHFrec3[i],sizeof(BPRecType),1,fp);
		i++;
	}
	
	f3->len3=i-2;fclose(fp);
	fp=fopen("read","rb");
	i=1;
	while(!feof(fp)) 
	{
		fread(&rf->ReadRec[i],sizeof(RRecType),1,fp);
		i++;
	}
	
	rf->len=i-2;fclose(fp);
	fp=fopen("bbff","rb");
	i=1;
	while(!feof(fp))
	{
		fread(&bbf->Bbook[i],sizeof(BbookRecType),1,fp);
		i++;
	}
	bbf->len=i-2;fclose(fp);
}

/*int BinSearch(BnoIdxFile bif,int key)
{ 
	int low,high,mid;
	low=1;
	high=bif.len;
	while(low<=high)
	{
		mid=(low+high)/2;
		if(key==bif.BnoIdx[mid].bno)
			return bif.BnoIdx[mid].RecNo;
		else if(key<bif.BnoIdx[mid].bno)
			high=mid-1;
		else low=mid+1;
	}
	return 0;
}*/

int BnameFind(LHFile1 lhf1,char key[])
{
	int i,k=0;
	for(i=1;i<=lhf1.len1;i++)
	{ 
		if(strcmp(key,lhf1.LHFrec1[i].bname)==0)
		{
			k=lhf1.LHFrec1[i].lhead;break;
		}
	}
	return k;
}

int BauthFind(LHFile2 lhf2,char key[])
{ 
	int i,k=0;
	for(i=1;i<=lhf2.len2;i++)
	{ 
		if(strcmp(key,lhf2.LHFrec2[i].author)==0)
		{
			k=lhf2.LHFrec2[i].lhead;break;
		}
	}
	return k;
}

int BnameFind1(LHFile3 lhf3,char key[])
{ 
	int i,k=0;
	for(i=1;i<=lhf3.len3;i++)
	{ 
		if(strcmp(key,lhf3.LHFrec3[i].press)==0)
		{
			k=lhf3.LHFrec3[i].lhead;break;
		}
	}
	return k;
}

void ShowRec(BookDbaseFile df,int i)
{
	//printf("书号   书名      作者名    出版社    分类号  可借数\n");
	printf("==============================================\n");
	printf("%d   %8s",df.BookDbase[i].bno,df.BookDbase[i].bname);
	printf(" %8s%12s",df.BookDbase[i].author,df.BookDbase[i].press);
	printf(" %6s",df.BookDbase[i].sortno);
	printf(" %4d\n",df.BookDbase[i].storenum-df.BookDbase[i].borrownum);
	printf("==============================================\n");
}

int BnameFile1(LHFile3 lhf3,char key[]);

void SearchBook(BookDbaseFile df,BnoIdxFile bif,LHFile1 f1,LHFile2 f2,LHFile3 f3)
{ 
	char sm[21],zz[9],cbs[11];
	int i,k,choose=1;
	int sh;
	while(choose>=1 && choose<=6)
	{ 
		printf("图书查询子系统\n");
		printf("------------------------\n");
		printf("1.书号   2.书名\n");
		printf("3.作者   4.出版社\n");
		printf("5.显示所有图书信息\n");
		printf("6.退出查询   \n");
		printf("------------------------\n");
		printf("请用户选择：      ");
		scanf("%d",&choose);
		switch(choose)
		{
		case 1:
			printf("输入书号:    ");scanf("%d",&sh);
			k=BinSearch(bif,sh);
			if(k==0) {
				printf("没有要查找的图书，请检查是否输入有错\n");
				break;
			}
			ShowRec(df,k);
			break;
		case 2:
			printf("输入书名:    ");scanf("%s",sm);
			k=BnameFind(f1,sm);
			if(k==0)
			{
				printf("没有要查找的图书，请检查是否输入有错\n");
				break;
			}
			printf("书号   书名      作者名    出版社    分类号  可借数\n");
			for(i=k;i;i=df.BookDbase[i].namenext)
				ShowRec(df,i);
			break;
		case 3:
			printf("输入作者名:    ");scanf("%s",zz);
			k=BauthFind(f2,zz);
			if(k==0)
			{
				printf("没有要查找的图书，请检查是否输入有错\n");
				break;
			}
			printf("书号   书名      作者名    出版社    分类号  可借数\n");
			for(i=k;i;i=df.BookDbase[i].authnext)
				ShowRec(df,i);
			break;
		case 4:
			printf("输入出版社:     ");scanf("%s",cbs);
			k=BnameFind1(f3,cbs);
			if(k==0)
			{
				printf("没有要查找的图书，请检查是否输入有错\n");
				break;
			}
			printf("书号   书名      作者名    出版社    分类号  可借数\n");
			for(i=k;i;i=df.BookDbase[i].prenext)
				ShowRec(df,i);
			break;
		case 5:
			printf("书号   书名      作者名    出版社    分类号  可借数\n");
			for(i=1;i<=df.len;i++)
				ShowRec(df,i);
			break;
		case 6: return;
		}
	}
}


void writefile(BookDbaseFile bf,BnoIdxFile bif,LHFile1 f1,LHFile2 f2,LHFile3 f3,ReadFile rf,BbookFile bbf)
{ 
	FILE *fp;int i;
	fp=fopen("book","wb");
	for(i=1;i<=bf.len;i++)
		fwrite(&bf.BookDbase[i],sizeof(BookRecType),1,fp);
		fclose(fp);
		fp=fopen("bidx","wb");
	for(i=1;i<=bif.len;i++)
		fwrite(&bif.BnoIdx[i],sizeof(BidxRecType),1,fp);
		fclose(fp);
		fp=fopen("nidx","wb");
	for(i=1;i<=f1.len1;i++)
		fwrite(&f1.LHFrec1[i],sizeof(BNRecType),1,fp);
		fclose(fp);
		fp=fopen("aidx","wb");
	for(i=1;i<=f2.len2;i++)
		fwrite(&f2.LHFrec2[i],sizeof(BARecType),1,fp);
		fclose(fp);
		fp=fopen("pidx","wb");
	for(i=1;i<=f3.len3;i++)
		fwrite(&f3.LHFrec3[i],sizeof(BPRecType),1,fp);
		fclose(fp);
		fp=fopen("read","wb");
	for(i=1;i<=rf.len;i++)
		fwrite(&rf.ReadRec[i],sizeof(RRecType),1,fp);
		fclose(fp);
		fp=fopen("bbff","wb");
	for(i=1;i<=bbf.len;i++)
		fwrite(&bbf.Bbook[i],sizeof(BbookRecType),1,fp);
		fclose(fp);
}
