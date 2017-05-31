
## lua_Reader

```c
typedef const char* (*lua_Reader) (lua_State *L, void *data, size_t *size);
```
The reader function used by `lua_load`. 
Every time it needs another piece of the chunk, `lua_load` calls the reader, passing along its `data` parameter.
The reader must return a pointer to a block of memory with a new piece of the chunk 
and set size to the block size.
The block must exist until the reader function is called again. 
To signal the end of the chunk, the reader must return NULL or set size to zero. 
The reader function may return pieces of any size greater than zero.

## lua_load [-0, +1, –]
```c
int lua_load(lua_State* L, lua_Reader reader, void* data, const char* chunkname, const char* mode);
```
Loads a Lua chunk without running it. 
If there are no errors, `lua_load` pushes the compiled chunk as a Lua function on top of the stack. 
Otherwise, it pushes an error message.

The return values of `lua_load` are:
- LUA_OK: no errors;
- LUA_ERRSYNTAX: syntax error during precompilation;
- LUA_ERRMEM: memory allocation error;
- LUA_ERRGCMM: error while running a `__gc` metamethod. 
  (This error has no relation with the chunk being loaded. It is generated by the garbage collector.)

The `lua_load` function uses a user-supplied reader function to read the chunk (see `lua_Reader`). 
The `data` argument is an opaque value passed to the reader function.

The chunkname argument gives a name to the chunk, 
which is used for error messages and in debug information (see §4.9).

`lua_load` automatically detects whether the chunk is text or binary 
and loads it accordingly (see program `luac`). 
The string mode works as in function `load`, 
with the addition that a `NULL` value is equivalent to the string "bt".

`lua_load` uses the stack internally, 
so the reader function must always leave the stack unmodified when returning.

If the resulting function has upvalues, its first upvalue is set to the value of the global environment 
stored at index `LUA_RIDX_GLOBALS` in the registry (see §4.5). 
When loading main chunks, this upvalue will be the `_ENV` variable (see §2.2). 
Other upvalues are initialized with `nil`.

## luaL_loadbuffer [-0, +1, –]

```c
int luaL_loadbuffer(lua_State *L, const char *buff, size_t sz, const char *name);
```                 
Equivalent to `luaL_loadbufferx` with `mode` equal to NULL.

## luaL_loadbufferx [-0, +1, –]

```c
int luaL_loadbufferx(lua_State *L, const char *buff, size_t sz, const char *name, const char *mode);
```
Loads a buffer as a Lua chunk. 
This function uses `lua_load` to load the chunk in the buffer pointed to by `buff` with size `sz`.

This function returns the same results as `lua_load`. 
`name` is the chunk name, used for debug information and error messages. 
The string mode works as in function `lua_load`.

## luaL_loadfile [-0, +1, e]

```c
int luaL_loadfile(lua_State *L, const char *filename);
```
Equivalent to `luaL_loadfilex` with mode equal to NULL.

## luaL_loadfilex [-0, +1, e]

```c
int luaL_loadfilex(lua_State *L, const char *filename, const char *mode);
```
Loads a file as a Lua chunk. This function uses lua_load to load the chunk in the file named filename. 
If filename is NULL, then it loads from the standard input. 
The first line in the file is ignored if it starts with a `#`.

The string mode works as in function `lua_load`.

This function returns the same results as `lua_load`, 
but it has an extra error code `LUA_ERRFILE` if it cannot open/read the file or the file has a wrong mode.

As `lua_load`, this function only loads the chunk; it does not run it.

## luaL_loadstring [-0, +1, –]

```c
int luaL_loadstring(lua_State *L, const char *s)
```
Loads a string as a Lua chunk. 
This function uses lua_load to load the chunk in the zero-terminated string `s`.

This function returns the same results as `lua_load`.

Also as `lua_load`, this function only loads the chunk; it does not run it.

lua_dump

[-0, +0, e]
int lua_dump (lua_State *L,
                        lua_Writer writer,
                        void *data,
                        int strip);
Dumps a function as a binary chunk. Receives a Lua function on the top of the stack and produces a binary chunk that, if loaded again, results in a function equivalent to the one dumped. As it produces parts of the chunk, lua_dump calls function writer (see lua_Writer) with the given data to write them.

If strip is true, the binary representation may not include all debug information about the function, to save space.

The value returned is the error code returned by the last call to the writer; 0 means no errors.

This function does not pop the Lua function from the stack.

# Lua Stdlib

**load (chunk [, chunkname [, mode [, env]]])**

Loads a chunk. If chunk is a string, the `chunk` is this string. 
If `chunk` is a function, `load` calls it repeatedly to get the chunk pieces. 
Each call to chunk must return a string that concatenates with previous results. 
A return of an empty string, `nil`, or no value signals the end of the chunk.

If there are no syntactic errors, returns the compiled chunk as a function; 
otherwise, returns `nil` plus the error message.

If the resulting function has upvalues, the first upvalue is set to the value of `env`, 
if that parameter is given, or to the value of the global environment. 
Other upvalues are initialized with `nil`. 
(When you load a main chunk, the resulting function will always have exactly one upvalue, 
the `_ENV` variable (see §2.2).
However, when you load a binary chunk created from a function (see `string.dump`), 
the resulting function can have an arbitrary number of upvalues.) 
All upvalues are fresh, that is, they are not shared with any other function.

`chunkname` is used as the name of the chunk for error messages and debug information (see §4.9). 
When absent, it defaults to chunk if chunk is a string, or to `"=(load)"` otherwise.

The string `mode` controls whether the chunk can be text or binary (that is, a precompiled chunk). 
It may be the string `"b"` (only binary chunks), `"t"` (only text chunks), or `"bt"` (both binary and text). 
The default is `"bt"`.

Lua does not check the consistency of binary chunks. 
Maliciously crafted binary chunks can crash the interpreter.

**loadfile ([filename [, mode [, env]]])**

Similar to `load`, but gets the chunk from file filename or from the standard input, if no file name is given.

load (chunk [, chunkname [, mode [, env]]])

Loads a chunk.

If chunk is a string, the chunk is this string. If chunk is a function, load calls it repeatedly to get the chunk pieces. Each call to chunk must return a string that concatenates with previous results. A return of an empty string, nil, or no value signals the end of the chunk.

If there are no syntactic errors, returns the compiled chunk as a function; otherwise, returns nil plus the error message.

If the resulting function has upvalues, the first upvalue is set to the value of env, if that parameter is given, or to the value of the global environment. Other upvalues are initialized with nil. (When you load a main chunk, the resulting function will always have exactly one upvalue, the _ENV variable (see §2.2). However, when you load a binary chunk created from a function (see string.dump), the resulting function can have an arbitrary number of upvalues.) All upvalues are fresh, that is, they are not shared with any other function.

chunkname is used as the name of the chunk for error messages and debug information (see §4.9). When absent, it defaults to chunk, if chunk is a string, or to "=(load)" otherwise.

The string mode controls whether the chunk can be text or binary (that is, a precompiled chunk). It may be the string "b" (only binary chunks), "t" (only text chunks), or "bt" (both binary and text). The default is "bt".

Lua does not check the consistency of binary chunks. Maliciously crafted binary chunks can crash the interpreter.




loadfile ([filename [, mode [, env]]])

Similar to load, but gets the chunk from file filename or from the standard input, if no file name is given.

next (table [, index])

Allows a program to traverse all fields of a table. Its first argument is a table and its second argument is an index in this table. next returns the next index of the table and its associated value. When called with nil as its second argument, next returns an initial index and its associated value. When called with the last index, or with nil in an empty table, next returns nil. If the second argument is absent, then it is interpreted as nil. In particular, you can use next(t) to check whether a table is empty.

The order in which the indices are enumerated is not specified, even for numeric indices. (To traverse a table in numerical order, use a numerical for.)

The behavior of next is undefined if, during the traversal, you assign any value to a non-existent field in the table. You may however modify existing fields. In particular, you may clear existing fields.


dofile ([filename])
Opens the named file and executes its contents as a Lua chunk. When called without arguments, dofile executes the contents of the standard input (stdin). Returns all values returned by the chunk. In case of errors, dofile propagates the error to its caller (that is, dofile does not run in protected mode).

luaL_loadbuffer

[-0, +1, –]
int luaL_loadbuffer (lua_State *L,
                     const char *buff,
                     size_t sz,
                     const char *name);
Equivalent to luaL_loadbufferx with mode equal to NULL.

luaL_loadbufferx

[-0, +1, –]
int luaL_loadbufferx (lua_State *L,
                      const char *buff,
                      size_t sz,
                      const char *name,
                      const char *mode);
Loads a buffer as a Lua chunk. This function uses lua_load to load the chunk in the buffer pointed to by buff with size sz.

This function returns the same results as lua_load. name is the chunk name, used for debug information and error messages. The string mode works as in function lua_load.

luaL_loadfile

[-0, +1, e]
int luaL_loadfile (lua_State *L, const char *filename);
Equivalent to luaL_loadfilex with mode equal to NULL.

luaL_loadfilex

[-0, +1, e]
int luaL_loadfilex (lua_State *L, const char *filename,
                                            const char *mode);
Loads a file as a Lua chunk. This function uses lua_load to load the chunk in the file named filename. If filename is NULL, then it loads from the standard input. The first line in the file is ignored if it starts with a #.

The string mode works as in function lua_load.

This function returns the same results as lua_load, but it has an extra error code LUA_ERRFILE if it cannot open/read the file or the file has a wrong mode.

As lua_load, this function only loads the chunk; it does not run it.

luaL_loadstring

[-0, +1, –]
int luaL_loadstring (lua_State *L, const char *s);
Loads a string as a Lua chunk. This function uses lua_load to load the chunk in the zero-terminated string s.

This function returns the same results as lua_load.

Also as lua_load, this function only loads the chunk; it does not run it.

luaL_callmeta

[-0, +(0|1), e]
int luaL_callmeta (lua_State *L, int obj, const char *e);
Calls a metamethod.

If the object at index obj has a metatable and this metatable has a field e, this function calls this field passing the object as its only argument. In this case this function returns true and pushes onto the stack the value returned by the call. If there is no metatable or no metamethod, this function returns false (without pushing any value on the stack).

luaL_dofile

[-0, +?, e]
int luaL_dofile (lua_State *L, const char *filename);
Loads and runs the given file. It is defined as the following macro:

     (luaL_loadfile(L, filename) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.

luaL_dostring

[-0, +?, –]
int luaL_dostring (lua_State *L, const char *str);
Loads and runs the given string. It is defined as the following macro:

     (luaL_loadstring(L, str) || lua_pcall(L, 0, LUA_MULTRET, 0))
It returns false if there are no errors or true in case of errors.


## lua_call [-(nargs+1),+nresults,e]
```c
void lua_call(lua_State* L, int nargs, int nresults);
#define lua_call(L,n,r) lua_callk(L, (n), (r), 0, NULL)
```
> To call a function you must use the following protocol: first, the function to be called is pushed onto the stack;
then, the arguments to the function are pushed in direct order; that is, the first argument is pushed first.
Finally you call `lua_call`; `nargs` is the number of arguments that you pushed onto the stack.
All arguments and the function value are popped from the stack when the function is called.
The function results are pushed onto the stack when the function returns.
The number of results is adjust to `nresults`, unless `nresults` is LUA_MULTRET.
In this case, all results from the function are pushed.
Lua takes care that the returned values to fit into the stack sapce.
The function results are pushed onto the stack in direct order (the first result is pushed first),
so that after the call the last result is on the top of the stack.
Any error inside the called function is propagated upwards (with a `longjmp`). 

用C API调用Lua函数需要遵循如下规则：首先将要调用的函数入栈；
然后将函数的参数，从第一个到最后一个依次入栈。
最后调用`lua_call`执行函数，`nargs`表示传入的参数个数。
函数执行时，传入的参数和栈底的函数会出栈。函数返回时，会将函数的结果入栈。
结束的个数会调整到`nresults`个，除非`nresults`的值为LUA_MULTRET，此时函数所有的结果都会入栈。
Lua会管理好结果的入栈操作。函数结果的入栈顺序是第一个结果先入栈，因此调用完后最后一个结果会在栈顶。
调用Lua函数任何错误都会通过`longjmp`向上传递。
如下所示的是与Lua函数等价的C API调用。

```c
// lua code:
a = f("how", t.x, 14)
// do same work with c:
lua_getglobal(L, "f");                  /* function to be called */
lua_pushliteral(L, "how");                       /* 1st argument */
lua_getglobal(L, "t");                    /* table to be indexed */
lua_getfield(L, -1, "x");        /* push result of t.x (2nd arg) */
lua_remove(L, -2);                  /* remove 't' from the stack */
lua_pushinteger(L, 14);                          /* 3rd argument */
lua_call(L, 3, 1);     /* call 'f' with 3 arguments and 1 result */
lua_setglobal(L, "a");                         /* set global 'a' */
```

> Note that the code above is *balanced*: at its end, the stack is back to its original configuration.
This is considered good programming practice.

注意的是上面的代码是*平衡的*：即在最后，栈的状态回到与原始状态一样。
这被认为是一种好的编程方法。

## lua_KFunction
```c
typedef int (*lua_KFunction)(lua_State* L, int status, lua_KContext ctx);
```
> Type for continuation functions (4.7).

> **lua_KContext**

> The type for continuation-function contexts. It must be a numeric type.
This type is deinfed as `intptr_t` when `intptr_t` is available, so that it can store pointers too.
Otherwise, it is defined as `ptrdiff_t`.

Continuation函数的类型（见4.7）。类型`lua_KContent`是Continuation函数的上下文。
它是一个数值类型，如果`intptr_t`存在则这个类型被定义成`intptr_t`，因此可以存储指针值。否则它被定义成`ptrdiff_t`。

## lua_callk [-(nargs+1),+nresults,e]
```c
void lua_callk(lua_State* L, int nargs, int nresults, lua_LContent ctx, lua_KFunction k);
```
> This function behaves exactly like `lua_call`, but allows the called function to yield (see 4.7).

该函数跟`lua_call`一样，但允许函数Yield。

### 代码追踪
```c
void lua_callk (lua_State* L, int nargs, int nresults, lua_KContext ctx, lua_KFunction k) {
  StkId func = L->top - (nargs+1);
  if (k != NULL && L->nny == 0) {  /* need to prepare continuation? */
    L->ci->u.c.k = k;  /* save continuation */
    L->ci->u.c.ctx = ctx;  /* save context */
    luaD_call(L, func, nresults);  /* do the call */
  }
  else  /* no continuation or no yieldable */
    luaD_callnoyield(L, func, nresults);  /* just do the call */
  adjustresults(L, nresults);
}
void luaD_call(lua_State* L, StkId func, int nresults) {
  if (++L->nCcalls >= LUAI_MAXCCALLS) stackerror(L); // LUAI_MAXCCALLS: 200
  if (!luaD_precall(L, func, nresults)) luaV_execute(L);
  L->nCcalls--;
}
void luaD_callnoyield(lua_State* L, StdId func, int nresults) {
  L->nyy++;
  luaD_call(L, func, nresults);
  L->nyy--;
}

// C函数调用流程
// 1. 获取C闭包或C函数的函数指针，C闭包的结构体如下：
typedef struct CClosure {
  GCObject* next; lu_byte tt; lu_byte marked; // GCObject CommandHeader
  lu_byte nupvalues; GCObject* gclist; // ClosureHeader
  lua_CFunction f;
  TValue upvalue[1];
} CClosure;
int luaD_precall(lua_State* L, StkId func, int nresults) {
  lua_CFunction f;         // C函数指针类型
  switch (ttype(func)) {
  case LUA_TCCL:           // 当前函数是C closure:
    f = clCvalue(func)->f; // 获取closure中存储的函数指针
    goto Cfunc;
  case LUA_TLCF:           // 当前函数是普通的C函数:
    f = fvalue(func);      // 获取这个C函数的指针luaValue.value_.f
    Cfunc: ...             // 见第2步
    return 1;
  }
}
// 2. 为当前函数准备Lua栈空间，保证可用空间大于20个(LUA_MINSTACK)
checkstackp(L, LUA_MINSTACK, func):
 if (L->stack_last - L->top <= 20) {
    ptrdiff_t offset = savestack(L, func); // 记住当前函数所在位置的偏移：(char*)func - (char*)L->stack
    luaC_checkGC(L); // 先做一步垃圾回收：if (G(L)->GCdebt > 0) luaC_step(L);
    luaD_growstack(L, 20); // 增长栈空间，增长后的大小为: max(请求的大小,当前栈大小的2倍)
    func = restorestack(L, offset); // 恢复当前函数在新栈中的位置：(TValue*)((char*)L->stack + offset)
  }
// 3. 准备当前函数调用信息
CallInfo *ci = next_ci(L);  // 进入调用链下一层，如果下一层为空则会新分配一个CallInfo对象
ci->nresults = nresults; ci->func = func; ci->callstatus = 0; // 设置返回结果个数、当前函数，并初始化函数调用状态
ci->top = L->top + LUA_MINSTACK; // 设置函数可以使用的栈空间范围[L->top, ci->top)
// 4. 如果设置了Hook，先调用luaD_hook
if (L->hookmask & LUA_MASKCALL) luaD_hook(L, LUA_HOOKCALL, -1);
// 5. 调用C函数
// 调用前，栈中保存有当前的函数、传入函数的参数、以及20个可用的栈空间
// 调用时，C函数会读取栈中的参数（调用lua_gettop可以获得参数的个数），最后将结果压入栈并返回结果的个数
int n = (*f)(L);
// 6. 函数调用完毕，返回调用链上一层，并调整栈内容
luaD_poscall(L, ci, L->top - n, n):
  // 传入的参数L->top-n表示函数第一个结果的位置，n是函数结果的个数
  L->ci = ci->previous; // 当前函数调用完毕，返回调用链上一层
  // 当前栈存储的当前函数调用相关的内容：[当前函数][nargs个函数参数][n个函数结果] => [nresults个函数结果]
  // firstresultpos指向第一个函数结果的位置，funcpos指向当前函数位置，
  // funcresults表示函数的结果个数，wantedresults表示调用者实际希望获得的结果个数
  // firstresultpos = L->top - n; funcpos = ci->func; funcresults = n; wantedresults = ci->nresults;
  // 将实际希望的结果拷贝到funcpos及之后，并忽略其他内容
  moveresults(L, firstresultpos, funcpos, funcresults, wantedresults); 
```

## Lua中的函数

函数只能访问全局变量和自己的局部变量（包括参数），但闭包还可以访问上文局部变量（即Lua中的上值）。
Lua有三种函数：用函数指针lua_CFunction表示的C函数；用CClosure表示的C闭包；以及用LClosure表示的Lua函数；
闭包和函数的本质区别是闭包可以访问上值，其结构体中维护了自己可以访问的上值列表。
```c
/* 约定名称列表
====================================================
 * 中文名词     英文解释              Lua表示
 * =========== ===================== =============
 * Lua值       Lua Value             TValue
 * Lua栈       Lua Virtual Stack     TValue的数组
 * C函数       C Function            lua_CFunction
 * C闭包       C Closure             CClosure
 * Lua函数     Lua Function          LClosure
 * 上值        Upvalue               TValue或UpVal
 * Lua线程     Lua Coroutine         lua_State
 * Lua状态     Lua State             lua_State
========================================================
*/
```

## 有关Lua函数的类型定义

```c
//Lua函数结构体
typedef struct LClosure {
  ClosureHeader;    //闭包公共头部
  struct Proto* p;  //TODO
  UpVal* upvals[1]; //上值的列表，有nupvalues个上值
} LClosure;
//闭包公共头部
#define ClosureHeader \
  CommonHeader; /*GC对象公共头部*/ lu_byte nupvalues; /*上值个数*/ GCObject *gclist /*TODO*/
//Lua函数上值结构体
typedef struct UpVal {
  TValue* v;        //指向Lua栈元素或Lua值 //TODO
  lu_mem refcount;  //引用计数 //TODO
  union {
    struct { UpVal* next; int touched; } open; //TODO
    TValue value;   //TODO
  } u;
} UpVal;
```

## 调用Lua函数

```c
//@[luaD_call]调用C或Lua函数
//前置条件：函数放在func位置，[func+1, L->top)放的是函数参数，并传入需要返回的结果个数nResults
//运行结果：[func, L->top)存放了调整后的nResults个函数结果
void luaD_call(lua_State* L, StkId func, int nResults) {
  if (++L->nCcalls >= LUAI_MAXCCALLS)   //函数嵌套调用深度必须小于LUAI_MAXCCALLS（200）@[lua-nest-call]
    stackerror(L);                      //否则抛出错误
  if (!luaD_precall(L, func, nResults)) //@[luaD_precall]调用C函数或为Lua函数调用作准备，返回0表示Lua函数
    luaV_execute(L);                    //@[luaV_execute]执行Lua函数
  L->nCcalls--;
}
int luaD_precall (lua_State *L, StkId func, int nresults) {
  CallInfo *ci;
  switch (ttype(func)) {                  //获取Lua值的类型（func->tt_ & 0x3F）
  case LUA_TLCL:                          //类型是Lua函数
    StkId base;                           //指向第1个参数
    Proto *p = clLvalue(func)->p;         //TODO
    int n = cast_int(L->top - func) - 1;  //函数参数[func+1, L->top)
    int fsize = p->maxstacksize;          //TODO
    checkstackp(L, fsize, func);          //TODO
    if (p->is_vararg != 1) {              //如果是固定参数函数
      for (; n < p->numparams; n++)       //传入的参数比实际的参数少（如果比实际参数多放在那里不管）
        setnilvalue(L->top++);            //用nil值填补少掉的参数
      base = func + 1;                    //保存第1个参数的指针
    } else {                              //如果是可变参数函数，接收所有的参数
      base = adjust_varargs(L, p, n);     //TODO
    }
    ci = next_ci(L);                      //进入函数调用链下一层（ci = L->ci = L->ci->next or new）
    ci->nresults = nresults;              //初始化当前调用信息：返回结果个数，当前函数的位置，第1个参数的位置
    ci->func = func;
    ci->u.l.base = base;
    L->top = ci->top = base + fsize;      //TODO
    lua_assert(ci->top <= L->stack_last); //TODO
    ci->u.l.savedpc = p->code;            //当前函数开始执行位置 //TODO
    ci->callstatus = CIST_LUA;            //初始化当前调用状态为调用Lua函数
    if (L->hookmask & LUA_MASKCALL)       //如果Lua状态设置了Call Hook掩码
      callhook(L, ci);                    //TODO
    return 0;                             //返回0表示当前是Lua函数
  }
}
```



## lua_pcall [-(nargs + 1), +(nresults|1), –]
```c
int lua_pcall(lua_State* L, int nargs, int nresults, int msgh);
#define lua_pcall(L,n,r,f) lua_pcallk(L, (n), (r), (f), 0, NULL)
```

Calls a function in protected mode.
Both `nargs` and `nresults` have the same meaning as in `lua_call`. 
If there are no errors during the call, `lua_pcall` behaves exactly like `lua_call`. 
However, if there is any error, `lua_pcall` catches it, pushes a single value on the stack (the error message), 
and returns an error code. Like `lua_call`, `lua_pcall` always removes the function and its arguments from the stack.

用保护模式调用Lua函数。参数`nargs`和`nresults`的含义跟`lua_call`一样。
如果函数调用那个没有错误发生，`lua_pcall`与`lua_call`完全一样。
如果发生错误，`lua_pcall`会获取它，将错误消息入栈，并将错误代码返回。
像`lua_call`一样，`lua_pcall`将传入的参数以及栈底的函数从栈中移除。

If `msgh` is 0, then the error message returned on the stack is exactly the original error message. 
Otherwise, `msgh` is the stack index of a message handler. (This index cannot be a pseudo-index.) 
In case of runtime errors, this function will be called with the error message 
and its return value will be the message returned on the stack by `lua_pcall`.

参数`msgh`如果是0，入栈的错误消息与原始的错误消息一样。
否则，`msgh`代表栈中错误处理函数的索引值（这个索引可以是伪索引）。
当运行时错误发生时，这个错误处理函数会对应的错误消息调用，而`lua_pcall`压入到栈的值是这个处理函数返回的值。

Typically, the message handler is used to add more debug information to the error message, 
such as a stack traceback. Such information cannot be gathered after the return of `lua_pcall`, 
since by then the stack has unwound.

一般的，错误处理函数用于为错误消息添加额外的debug信息，如栈的追踪。
这些信息不能在`lua_pcall`执行完后去收集，因为此时Lua栈已经执行了展开操作回到以前的状态。

`lua_pcall`函数会返回的值如下（定义在lua.h中）。

The `lua_pcall` function returns one of the following constants (defined in lua.h):
- LUA_OK (0): success.
- LUA_ERRRUN: a runtime error.
- LUA_ERRMEM: memory allocation error. For such errors, Lua does not call the message handler.
- LUA_ERRERR: error while running the message handler.
- LUA_ERRGCMM: error while running a `__gc` metamethod. 
  (This error typically has no relation with the function being called.)

## lua_pcallk [-(nargs + 1), +(nresults|1), –]
```c
int lua_pcallk(lua_State* L, int nargs, int nresults, int msgh, lua_KContext ctx, lua_KFunction k);
```

This function behaves exactly like `lua_pcall`, but allows the called function to yield (see §4.7).

这个函数与`lua_pcall`的行为一样，除了允许被调用的函数yield。

### 代码追踪
```c
//@[lua_pcallk]执行可错误恢复的保护调用，如果提供了k函数，被调函数可yield
int lua_pcallk(lua_State* L, int nargs, int nresults, int errfunc,
  lua_KContext ctx, lua_KFunction k) {
  struct CallS c;                             //该结构记录被调函数在Lua栈中的元素指针，以及函数需返回的结果个数
  int status;                                 //记录状态
  ptrdiff_t func;                             //该变量记录错误处理函数在Lua栈中的绝对索引
  lua_lock(L);                                //TODO
  //如果有k函数，当前调用状态（L->ci->callstatus）不能有CIST_LUA标志 //TODO
  api_check(L, k == NULL || !isLua(L->ci), "cannot use continuations inside hooks");
  api_checknelems(L, nargs+1);                //TODO
  api_check(L, L->status == LUA_OK, "cannot do calls on non-normal thread"); //Lua状态必须是LUA_OK
  //结果个数如果不是LUA_MULTRET，当前函数可使用空间（L->ci->top - L->top）不能小于（nresults - nargs）
  checkresults(L, nargs, nresults);           
  if (errfunc == 0)                           //如果没有提供错误处理函数
    func = 0;                                 //其绝对索引记为0
  else {                                      //如果提供了错误处理函数
    StkId o = index2addr(L, errfunc);         //将错误函数转换成栈元素指针 //TODO @[index2addr]
    api_checkstackindex(L, errfunc, o);       //TODO
    func = savestack(L, o);                   //记录错误函数在Lua栈中的绝对索引（o - L->stack）
  }
  c.func = L->top - (nargs+1);                //被调函数对应的Lua栈元素指针
  if (k == NULL || L->nny > 0) {              //没有k函数或者不能yield，执行不能yield的保护调用 //TODO
    c.nresults = nresults;                    //记录返回结果个数
    //执行保护调用@[luaD_pcall]，保护的函数是f_call，最后两个参数传递被调函数和错误函数的绝对索引值
    //f_call执行不yield的调用：luaD_callnoyield(L, c->func, c->nresults)
    status = luaD_pcall(L, f_call, &c, savestack(L, c.func), func); 
  }
  else {  /* prepare continuation (call is already protected by 'resume') */
    CallInfo *ci = L->ci;                    //记录调用信息用于错误恢复：
    ci->u.c.k = k;                           //k函数
    ci->u.c.ctx = ctx;                       //k上下文
    ci->extra = savestack(L, c.func);        //被调函数的绝对索引值
    ci->u.c.old_errfunc = L->errfunc;        //旧错误处理函数的绝对索引值
    L->errfunc = func;                       //新错误处理函数的绝对索引值
    setoah(ci->callstatus, L->allowhook);    //TODO
    ci->callstatus |= CIST_YPCALL;           //增加可yield保护调用标志
    luaD_call(L, c.func, nresults);          //调用被调函数 //TODO
    ci->callstatus &= ~CIST_YPCALL;          //清除可yield保护调用标志
    L->errfunc = ci->u.c.old_errfunc;        //恢复旧的错误处理函数
    status = LUA_OK;                         //如果能到达这里，表示没有错误
  }
  adjustresults(L, nresults);                //将结果调整成所需的个数 @[adjustresults]
  lua_unlock(L);                             //TODO
  return status;                             //返回最后的状态
}

//@[luaD_pcall]
int luaD_pcall(lua_State* L, Pfunc pfunc, void* u, ptrdiff_t funcidx, ptrdiff_t ef) {
  int status;
  CallInfo *old_ci = L->ci;                   //保存旧的调用信息指针
  lu_byte old_allowhooks = L->allowhook;      //保存旧的allowhook
  unsigned short old_nny = L->nny;            //保存旧的nny
  ptrdiff_t old_errfunc = L->errfunc;         //保存旧的错误处理函数
  L->errfunc = ef;                            //记录当前错误处理函数绝对索引值
  status = luaD_rawrunprotected(L, pfunc, u); //执行保护调用 @[luaD_rawrunprotected]
  if (status != LUA_OK) {                     //如果保护调用发生了错误
    StkId funcpos = restorestack(L, funcidx); //使用被调函数的索引，获取被调函数在当前Lua栈中的栈元素指针
    luaF_close(L, funcpos);  /* close possible pending closures */ //TODO
    seterrorobj(L, status, funcpos);          //将错误信息保存到被调函数所在位置 //TODO
    L->ci = old_ci;                           //恢复旧的调用信息指针
    L->allowhook = old_allowhooks;            //恢复旧的allowhook
    L->nny = old_nny;                         //恢复旧的nny
    luaD_shrinkstack(L);                      //执行一次Lua栈缩减 //TODO
  }
  L->errfunc = old_errfunc;                   //最后恢复旧错误处理函数
  return status;                              //并返回最终状态
}

//@[luaD_rawrunprotected]
int luaD_rawrunprotected(lua_State* L, Pfunc f, void* ud) {
  unsigned short oldnCcalls = L->nCcalls; //记录旧的nCcalls
  struct lua_longjmp lj;
  lj.status = LUA_OK;        //初始化longjmp状态为LUA_OK @[lua-longjmp]
  lj.previous = L->errorJmp; //记录旧的errorJmp
  L->errorJmp = &lj;         //使用新的errorJmp
  LUAI_TRY(L, &lj,           //保护调用(*f)(L,ud)，相当于：
    (*f)(L, ud);             //if (setjmp((&lj)->b) == 0) { (*f)(l,ud); }
  );                         //第一次调用setjmp会返回0执行函数f，如果f中发生错误longjmp，setjmp会返回非0值
  L->errorJmp = lj.previous; //恢复旧的errorJmp
  L->nCcalls = oldnCcalls;   //恢复旧的nCcalls
  return lj.status;          //返回longjmp的最终状态
}
```

---------------------------------------------------------------------------------------------------

pcall (f [, arg1, ···])

Calls function f with the given arguments in protected mode. This means that any error inside f is not propagated; instead, pcall catches the error and returns a status code. Its first result is the status code (a boolean), which is true if the call succeeds without errors. In such case, pcall also returns all results from the call, after this first result. In case of any error, pcall returns false plus the error message.


xpcall (f, msgh [, arg1, ···])

This function is similar to pcall, except that it sets a new message handler msgh. 