```c
//! 
lua_State *L = luaL_newstate(); //创建一个state
lua_close(L); 					//关闭state

luaL_loadfile(L,"hello.lua");	//加载lua文件
lua_pcall(L,0,0,0);				//运行lua文件

lua_pushcclosure(L, func, 0); 	//创建并压入一个闭包
lua_createtable(L, 0, 0);       //新建并压入一个表
lua_pushnumber(L, 343);      	//压入一个数字
lua_pushstring(L, “mystr”);   	//压入一个字符串

    
lua_isstring(L,1));					        //判断是否可以转为string 
int   lua_gettop (lua_State *L);            //返回栈顶索引（即栈长度）  
void  lua_settop (lua_State *L, int idx);   //可以用lua_settop(0)来清空栈               
void  lua_pushvalue (lua_State *L, int idx);//将idx索引上的值的副本压入栈顶  
void  lua_remove (lua_State *L, int idx);   //移除idx索引上的值  
void  lua_insert (lua_State *L, int idx);   //弹出栈顶元素，并插入索引idx位置  
void  lua_replace (lua_State *L, int idx);  //弹出栈顶元素，并替换索引idx位置的值

//! 引用机制
int luaL_ref (lua_State *L, int t)
void luaL_unref (lua_State *L, int t, int ref)
```

```c

```

