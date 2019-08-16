# utl-bigint-in-base-sas-macro
    Bigint in base sas macro                                                                                         
                                                                                                                     
       Three Solutions                                                                                               
                                                                                                                     
           a. macro  (bigint is bigint compliant)                                                                    
                                                                                                                     
           b. fedsql (output is SAS character variable)                                                              
                                                                                                                     
           c. Hand coded adding non-negative integers (up to 32766 digits)                                           
              Paul Dorfman                                                                                           
              sashole@bellsouth.net                                                                                  
                                                                                                                     
    Problem: 9458669011700000 + 7783 does not equal 9458669011707783                                                 
                                                                                                                     
    SAS Forum                                                                                                        
    https://tinyurl.com/yxwb3md8                                                                                     
    https://communities.sas.com/t5/SAS-Programming/Convert-large-number/m-p/581328                                   
                                                                                                                     
    Patrick                                                                                                          
    https://communities.sas.com/t5/user/viewprofilepage/user-id/12447                                                
                                                                                                                     
    *                _     _                                                                                         
     _ __  _ __ ___ | |__ | | ___ _ __ ___                                                                           
    | '_ \| '__/ _ \| '_ \| |/ _ \ '_ ` _ \                                                                          
    | |_) | | | (_) | |_) | |  __/ | | | | |                                                                         
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|                                                                         
    |_|                                                                                                              
    ;                                                                                                                
                                                                                                                     
    data b;                                                                                                          
    x='9458669011700000';                                                                                            
    y=input(x,best32.) + 7783;                                                                                       
    z=put(y,16.);                                                                                                    
    put y= best32.;                                                                                                  
    keep x y z;                                                                                                      
    run;                                                                                                             
                                                                                                                     
    Current Result                                                                                                   
                                                                                                                     
    z = 9458669011707784                                                                                             
                                                                                                                     
    *          _       _   _                                                                                         
     ___  ___ | |_   _| |_(_) ___  _ __                                                                              
    / __|/ _ \| | | | | __| |/ _ \| '_ \                                                                             
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                                            
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                                            
                                                                                                                     
    ;                                                                                                                
                                                                                                                     
    *                                                                                                                
      __ _     _ __ ___   __ _  ___ _ __ ___                                                                         
     / _` |   | '_ ` _ \ / _` |/ __| '__/ _ \                                                                        
    | (_| |_  | | | | | | (_| | (__| | | (_) |                                                                       
     \__,_(_) |_| |_| |_|\__,_|\___|_|  \___/                                                                        
                                                                                                                     
    ;                                                                                                                
                                                                                                                     
    %let x=9458669011700000;                                                                                         
    %let y=%eval(&x + 7783);                                                                                         
    %put &=y;                                                                                                        
                                                                                                                     
    Y=9458669011707783                                                                                               
                                                                                                                     
    *_         __          _           _                                                                             
    | |__     / _| ___  __| |___  __ _| |                                                                            
    | '_ \   | |_ / _ \/ _` / __|/ _` | |                                                                            
    | |_) |  |  _|  __/ (_| \__ \ (_| | |                                                                            
    |_.__(_) |_|  \___|\__,_|___/\__, |_|                                                                            
                                    |_|                                                                              
    ;                                                                                                                
                                                                                                                     
    * I create an empty dataset using my autoexec at sas invocation;                                                 
    * it is useful here                                                                                              
                                                                                                                     
    data empty;                                                                                                      
       retain empty .;                                                                                               
    run;quit;                                                                                                        
                                                                                                                     
    proc fedsql;                                                                                                     
      select cast(cast('9458669011700000' as bigint) + 7783 as char(16)) as z                                        
      from empty                                                                                                     
      ;                                                                                                              
    quit;                                                                                                            
                                                                                                                     
    Z                                                                                                                
    ----------------                                                                                                 
    9458669011707783                                                                                                 
                                                                                                                     
                                                                                                                     
    *         _                     _                 _          _                                                   
      ___    | |__   __ _ _ __   __| |   ___ ___   __| | ___  __| |                                                  
     / __|   | '_ \ / _` | '_ \ / _` |  / __/ _ \ / _` |/ _ \/ _` |                                                  
    | (__ _  | | | | (_| | | | | (_| | | (_| (_) | (_| |  __/ (_| |                                                  
     \___(_) |_| |_|\__,_|_| |_|\__,_|  \___\___/ \__,_|\___|\__,_|                                                  
                                                                                                                     
    ;                                                                                                                
                                                                                                                     
    In a simple case like this (adding non-negative integers), it takes all but a                                    
    few minutes to code by mindlessly emulating the process of doing it "by hand":                                   
                                                                                                                     
    data _null_ ;                                                                                                    
      retain _b 10 ; * base 10 ;                                                                                     
      x = "9458669011700000" ;                                                                                       
      y = "7783" ;                                                                                                   
      length sum $ 32 ;                                                                                              
      _x = left (reverse (x)) ;                                                                                      
      _y = left (reverse (y)) ;                                                                                      
      _c = 0 ;                                                                                                       
      do _i = 1 by 1 to max (length (_x), length (_y)) ;                                                             
        _dx = input (char (_x, _i), 1.) ;                                                                            
        _dy = input (char (_y, _i), 1.) ;                                                                            
        _dz = sum (_dx, _dy, 0) ;                                                                                    
        _ds = _c + mod (_dz, _b) ;                                                                                   
        _c = floor (divide (_ds + _c, _b)) ;                                                                         
        substr (sum, _i) = put (_ds, 1.) ;                                                                           
      end ;                                                                                                          
      if _c then substr (sum, _i) = "1" ;                                                                            
      sum = left (reverse (sum)) ;                                                                                   
      put sum= ;                                                                                                     
    run ;                                                                                                            
                                                                                                                     
    The advantages are that it requires nothing but the DATA step, elementary                                        
    to code, and can handle digit strings of any length - oh, well, up to 32766,                                     
    but that far exceeds what BIGINT can muster. The disadvantage is that since                                      
    the code above computes one digit at a time (as we would do by hand),                                            
    it's not going to win any speed race prizes.                                                                     
                                                                                                                     
    Note, though, that the base_B doesn't have to be 10: Instead, it can be                                          
    chosen to be 10**2, 10**3, and so on all the way to 10**15, since at this                                        
    "digit size", SAS still has enough integer numeric precision for addition.                                       
    Accordingly, instead of stepping one place at a time, we would then step 2, 3,                                   
    and so on, places. Of course, BY 1 and the in/format, and SUBSTR widths would have t                             
    o be increased correspondingly. In this case, if _B were chosen at 10**8 or                                      
    more, the addition would be completed in just 2 substring-grabbing steps.                                        
    At the "digit size" of 10**15, computing a sum of two addends 100 digits long                                    
    would require only 6 iterations of a DO loop like above (with BY 15).                                            
                                                                                                                     
                                                                                                                     
                                                                                                                     
