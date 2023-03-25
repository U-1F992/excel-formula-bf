# excel-formula-bf

Brainf*ck interpreter written with Excel formula

Here's [playground](https://docs.google.com/spreadsheets/d/1XFHzKRiytyjLT7nAq8xEDqdVjBUVlPkoSPIeihb3l1Y/edit#gid=0) on Google Sheets.

## Blog

https://zenn.dev/u1f992/articles/a99aa870531ec9

## Formula

```
=
LET(UPDATEARRAY,LAMBDA(base,row,col,val,
  MAKEARRAY(ROWS(base),COLUMNS(base),LAMBDA(r,c,
    IF(AND(r=row,c=col),
      val,
      INDEX(base,r,c)
    )
  ))
),
LET(UPDATEARRAY.ADD,LAMBDA(base,row,col,val,
  UPDATEARRAY(base,row,col,INDEX(base,row,col)+val)
),
LET(UPDATEARRAY.INCR,LAMBDA(base,row,col,
  UPDATEARRAY.ADD(base,row,col,+1)
),
LET(UPDATEARRAY.DECR,LAMBDA(base,row,col,
  UPDATEARRAY.ADD(base,row,col,-1)
),

LET(CHARAT,LAMBDA(str,pos,
  MID(str,pos,1)
),
LET(CHARAT.HEAD,LAMBDA(str,
  CHARAT(str,1)
),

LET(REMOVE,LAMBDA(str,pos,cnt,
  LEFT(str,pos-1)&RIGHT(str,LEN(str)-(pos+cnt-1))
),
LET(REMOVE.HEAD,LAMBDA(str,cnt,
  REMOVE(str,1,cnt)
),

LET(CORRFINDER,LAMBDA(looking_for,corresponding,forward,
  LET(next,IF(forward,1,-1),

  LAMBDA(src,pos,
    LET(_,LAMBDA(F,pos,depth,
      LET(token,CHARAT(src,pos),

      SWITCH(token,          
        looking_for,
          IF(depth=1,
            pos,
            F(F,pos+next,depth-1)
          ),

        corresponding,
          F(F,pos+next,depth+1),

        F(F,pos+next,depth)
      )
    )),
    _(_,pos+next,1)
  ))
)),
LET(CORRRBRACKET,CORRFINDER("]", "[", TRUE),
LET(CORRLBRACKET,CORRFINDER("[", "]", FALSE),

LAMBDA(src,mem,in,out,
  LET(_,LAMBDA(F,pos,mem,ptr,in,out,
    LET(JUMP,LAMBDA(pos,mem,ptr,in,out,
      F(F,pos,mem,ptr,in,out)
    ),
    LET(JUMP.NEXT,LAMBDA(mem,ptr,in,out,
      JUMP(pos+1,mem,ptr,in,out)
    ),
    LET(JUMP.PASSTHRU,LAMBDA(
      JUMP.NEXT(mem,ptr,in,out)
    ),

    LET(PUTCHAR,LAMBDA(
      out&CHAR(INDEX(mem,ptr,1))
    ),
    LET(GETCHAR,LAMBDA(
      UPDATEARRAY(mem,ptr,1,CODE(CHARAT.HEAD(in)))
    ),

    IF(LEN(src)<pos,
      out,
      
      LET(token,CHARAT(src,pos),

      SWITCH(token,
        ">",
          JUMP.NEXT(mem,ptr+1,in,out),

        "<",
          JUMP.NEXT(mem,ptr-1,in,out),

        "+",
          JUMP.NEXT(UPDATEARRAY.INCR(mem,ptr,1),ptr,in,out),

        "-",
          JUMP.NEXT(UPDATEARRAY.DECR(mem,ptr,1),ptr,in,out),

        ".",
          JUMP.NEXT(mem,ptr,in,PUTCHAR()),

        ",",
          JUMP.NEXT(GETCHAR(),ptr,REMOVE.HEAD(in,1),out),
        
        "[",
          IF(INDEX(mem,ptr,1)=0,
            JUMP(CORRRBRACKET(src,pos),mem,ptr,in,out),
            JUMP.PASSTHRU()
          ),

        "]",
          IF(INDEX(mem,ptr,1)<>0,
            JUMP(CORRLBRACKET(src,pos),mem,ptr,in,out),
            JUMP.PASSTHRU()
          ),
        
        JUMP.PASSTHRU()
      )
    ))
  )))))),
  _(_,1,mem,1,in,out)
))
)))))))))))(B4,MAKEARRAY(64,1,LAMBDA(r,c,0)),B7,"")
```
