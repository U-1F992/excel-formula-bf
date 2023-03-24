# excel-formula-bf

Brainf*ck interpreter written with Excel formula

Here's [playground](https://docs.google.com/spreadsheets/d/1XFHzKRiytyjLT7nAq8xEDqdVjBUVlPkoSPIeihb3l1Y/edit#gid=0) on Google Sheets.

## Blog

https://zenn.dev/u1f992/articles/a99aa870531ec9

## Formula

```
=
LET(UPDATEARRAY,LAMBDA(base,row,column,value,
  MAKEARRAY(ROWS(base),COLUMNS(base),LAMBDA(r,c,
    IF(AND(r=row,c=column),
      value,
      INDEX(base,r,c)
    )
  ))
),

LET(CORRFINDER,LAMBDA(looking_for,corresponding,forward,
  LET(next,IF(forward,1,-1),

  LAMBDA(src,pos,
    LET(_,LAMBDA(F,pos,depth,
      LET(token,MID(src,pos,1),

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

LET(INTERPRET,LAMBDA(src,input_stream,
  LET(_,LAMBDA(F,pos,arr,idx,input_stream,output_stream,
    LET(JUMPTONEXT,LAMBDA(arr,idx,input_stream,output_stream,
      F(F,pos+1,arr,idx,input_stream,output_stream)
    ),
    LET(JUMPTORBRACKET,LAMBDA(arr,idx,input_stream,output_stream,
      F(F,CORRRBRACKET(src,pos),arr,idx,input_stream,output_stream)
    ),
    LET(JUMPTOLBRACKET,LAMBDA(arr,idx,input_stream,output_stream,
      F(F,CORRLBRACKET(src,pos),arr,idx,input_stream,output_stream)
    ),

    IF(pos=LEN(src)+1,
      output_stream,
      
      LET(token,MID(src,pos,1),

      SWITCH(token,
        ">",
          JUMPTONEXT(arr,idx+1,input_stream,output_stream),

        "<",
          JUMPTONEXT(arr,idx-1,input_stream,output_stream),

        "+",
          JUMPTONEXT(UPDATEARRAY(arr,idx,1,INDEX(arr,idx,1)+1),idx,input_stream,output_stream),

        "-",
          JUMPTONEXT(UPDATEARRAY(arr,idx,1,INDEX(arr,idx,1)-1),idx,input_stream,output_stream),

        ".",
          JUMPTONEXT(arr,idx,input_stream,CONCAT(output_stream,CHAR(INDEX(arr,idx,1)))),

        ",",
          JUMPTONEXT(UPDATEARRAY(arr,idx,1,CODE(MID(input_stream,1,1))),idx,RIGHT(input_stream,LEN(input_stream)-1),output_stream),
        
        "[",
          IF(INDEX(arr,idx,1)=0,
            JUMPTORBRACKET(arr,idx,input_stream,output_stream),
            JUMPTONEXT(arr,idx,input_stream,output_stream)
          ),

        "]",
          IF(INDEX(arr,idx,1)<>0,
            JUMPTOLBRACKET(arr,idx,input_stream,output_stream),
            JUMPTONEXT(arr,idx,input_stream,output_stream)
          ),
        
        JUMPTONEXT(arr,idx,input_stream,output_stream)
      )
    ))
  )))),
  _(_,1,MAKEARRAY(32,1,LAMBDA(r,c,0)),1,input_stream,"")
)),

INTERPRET(B4,B7)

)))))
```
