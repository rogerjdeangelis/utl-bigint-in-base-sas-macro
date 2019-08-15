# utl-bigint-in-base-sas-macro
Bigint in base sas macro

    SAS Forum: Bigint in base sas macro                                              
                                                                                     
       Two Solutions                                                                 
           a. macro  (bigint is bigint compliant)                                    
           b. fedsql (output is SAS character variable)                              
                                                                                     
                                                                                     
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
                                                                                     
