title: LiteIM/FreePlus的取IP代码
link: http://www.54chen.com/web-ral/liteim-freeplusu002639s-get-ip-code.html
author: admin
description: 
post_id: 36
created: 2007/10/29 18:15:53
created_gmt: 2007/10/29 10:15:53
comment_status: open
post_name: liteim-freeplusu002639s-get-ip-code
status: publish
post_type: post

# LiteIM/FreePlus的取IP代码

这是LiteIM/FreePlus的取IP代码,感兴趣的朋友看看吧.
    runjin的是汇编版本,这是c版本,希望有更多的人能读懂.后来者,愿它对你有益.闪了.
    源码：
    static const DWORD UnknownSig=0x1E3A86BA;
    static const LPSTR  szQQUSER_DYNAMIC_DATA="QQUSER_DYNAMIC_DATA";
    typedef void( *pPrepareDynData)(LPVOID IQQData,LPSTR szCmd,const DWORD* szSig,LPVOID* pDynamicData);
    VOID PrepareData(LPVOID IQQData,LPVOID* pDynamicData)
    {
        pPrepareDynData PrepareDynData=(pPrepareDynData)*(LONG *)(*(LONG *)IQQData+0x54);
        PrepareDynData(IQQData,szQQUSER_DYNAMIC_DATA,&UnknownSig,pDynamicData);
    }
    
    DWORD GetDyncDWORD(LPVOID DynamicData, int iOffset,LPCSTR szCmd)
    {
        DWORD dwRet=0;
        __asm
        {
            lea eax,dwRet
            push eax
            push szCmd
            mov eax, DynamicData
            push eax
            mov ecx,[eax]
            mov eax,ecx
            add eax,iOffset
            call [eax]
        }
        return dwRet;
    }
    
    static const LPSTR  szdwRecentIP="dwRecentIP";
    static const LPSTR  szdwIP="dwIP";
    static const LPSTR  szdwC2CIP="dwC2CIP";
    DWORD GetIP(DWORD Uin)
    {
        DWORD dwRet=0;
        LPVOID IQQData=NULL;
        if (!GetFriendQQData(g_IQQCore,Uin,&IQQData)) return dwRet;
        LPVOID DynamicData=NULL;
        PrepareData(IQQData,&DynamicData);
        if (NULL==DynamicData) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x34,szdwC2CIP);//ecx+18
        if (dwRet) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x34,szdwIP);
        if (dwRet) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x34,szdwRecentIP);
        return dwRet;
    }
    
    static const LPSTR  szwRecentPort="wRecentPort";
    static const LPSTR  szwPort= "wPort";
    static const LPSTR  szwC2CPort= "wC2CPort";
    DWORD GetPort(DWORD Uin)
    {
        DWORD dwRet=0;
        LPVOID IQQData=NULL;
        if (!GetFriendQQData(g_IQQCore,Uin,&IQQData)) return dwRet;
        LPVOID DynamicData=NULL;
        PrepareData(IQQData,&DynamicData);
        if (NULL==DynamicData) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x30,szwC2CPort);//ecx+14
        if (dwRet) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x30,szwPort);
        if (dwRet) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x30,szwRecentPort);
        return dwRet;
    }
    
    static const LPSTR  szwProcotol = "wProcotol";
    DWORD GetVer(DWORD Uin)
    {
        DWORD dwRet=0;
        LPVOID IQQData=NULL;
        if (!GetFriendQQData(g_IQQCore,Uin,&IQQData)) return dwRet;
        LPVOID DynamicData=NULL;
        PrepareData(IQQData,&DynamicData);
        if (NULL==DynamicData) return dwRet;
        dwRet=GetDyncDWORD(DynamicData,0x30,szwProcotol);
        return dwRet;
    }
    出处：ye-soft 作者：asaki