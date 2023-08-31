.. _API:

=========
Functions
=========

The main header `mujoco.h <https://github.com/deepmind/mujoco/blob/main/include/mujoco/mujoco.h>`_ exposes a large
number of functions. However the functions that most users are likely to need are a small fraction.

主要的头文件 mujoco.h 定义了大量的函数。然而，大多数用户可能需要的函数只占其中的一小部分。

API function can be classified as:

API函数可以分类为：

- :ref:`Parse and compile<Parseandcompile>` an :ref:`mjModel` from XML files and assets.

  :ref:`Parse and compile<Parseandcompile>` 从XML文件和资源中解析和编译一个 :ref:`mjModel` 。
- :ref:`Main simulation<Mainsimulation>` entry points, including :ref:`mj_step`.

  :ref:`Main simulation<Mainsimulation>` 入口点，包括 :ref:`mj_step`
- :ref:`Support<Support>` functions requiring :ref:`mjModel` and :ref:`mjData`.

  :ref:`Support<Support>` 需要 :ref:`mjModel` and :ref:`mjData` 的函数.
- :ref:`Components<Components>` of the simulation pipeline, called from :ref:`mj_step`, :ref:`mj_forward` and :ref:`mj_inverse`.

  :ref:`Components<Components>` 仿真管线组件，在:ref:`mj_step`、:ref:`mj_forward` 和:ref:`mj_inverse`中调用
- :ref:`Sub components<Subcomponents>` of the simulation pipeline.

  :ref:`Sub components<Subcomponents>` 仿真管线子组件.
- :ref:`Ray collisions<Raycollisions>`.

  :ref:`Ray collisions<Raycollisions>` 射线碰撞
- :ref:`Printing<Printing>` of various quantities.

  :ref:`Printing<Printing>` 各种数据的打印
- :ref:`Virtual file system<Virtualfilesystem>`, used to load assets from memory.

  :ref:`Virtual file system<Virtualfilesystem>`,虚拟文件系统，用于从内存加载资源.
- :ref:`Initialization<Initialization>` of data structures.

  :ref:`Initialization<Initialization>` 数据结构的初始化。
- :ref:`Abstract interaction<Interaction>`: mouse control of cameras and perturbations.

  :ref:`Abstract interaction<Interaction>` 交互抽象函数：鼠标控制相机和扰动。.
- :ref:`Abstract Visualization<Visualization-api>`.

  :ref:`Abstract Visualization<Visualization-api>` 可视化抽象函数
- :ref:`OpenGL rendering<OpenGLrendering>`.OpenGL渲染
- :ref:`UI framework<UIframework>`.UI框架
- :ref:`Error and memory<Errorandmemory>`.错误和内存
- :ref:`Aliases for C standard math<Standardmath>` functions c标准数学函数的另外实现.
- :ref:`Vector math<Vectormath>`.向量运算 
- :ref:`Quaternions<Quaternions>`.四元数
- :ref:`Poses transformations<Poses>`.姿态变化
- :ref:`Matrix decompositions and solvers<Decompositions>`.矩阵分解与求解器
- :ref:`Miscellaneous<Miscellaneous>` functions. 其他函数
- :ref:`Derivatives<Derivatives-api>`.导数
- :ref:`Plugin<Plugins-api>` related functions.插件相关函数
- :ref:`Macros<Macros>`.宏定义

.. TODO(b/273075045): Better category-label namespacing.

.. include:: functions.rst

.. _Macros:

Macros
^^^^^^


.. _mjMARKSTACK:

mjMARKSTACK
~~~~~~~~~~~

.. code-block:: C

   #define mjMARKSTACK int _mark = d->pstack;

This macro is helpful when using the MuJoCo stack in custom computations. It works together with the next macro and the
:ref:`mj_stackAlloc` family of functions, and assumes that mjData\* d is defined. The use pattern is this:

这个宏在使用自定义计算时很有帮助。它与下一个宏和 :ref:`mj_stackAlloc` 函数系列一起使用，假设已经定义了 mjData\* d。使用模式如下：

.. code-block:: C

   mjMARKSTACK;
   mjtNum* temp = mj_stackAllocNum(d, 100);
   // ... use temp as needed
   mjFREESTACK;


.. _mjFREESTACK:

mjFREESTACK
~~~~~~~~~~~

.. code-block:: C

   #define mjFREESTACK d->pstack = _mark;

Reset the MuJoCo stack pointer to the variable \_mark, normally saved by mjMARKSTACK.

重置MuJoCo的堆栈指针到变量_mark，通常由mjMARKSTACK保存。

.. _mjDISABLED:

mjDISABLED
~~~~~~~~~~

.. code-block:: C

   #define mjDISABLED(x) (m->opt.disableflags & (x))

Check if a given standard feature has been disabled via the physics options, assuming mjModel\* m is defined. x is of
type :ref:`mjtDisableBit`.

检查给定的标准功能是否已通过物理选项禁用，假定已定义mjModel\* m。x的类型是 :ref:`mjtDisableBit`。

.. _mjENABLED:

mjENABLED
~~~~~~~~~

.. code-block:: C

   #define mjENABLED(x) (m->opt.enableflags & (x))

Check if a given optional feature has been enabled via the physics options, assuming mjModel\* m is defined. x is of
type :ref:`mjtEnableBit`.

检查给定的可选功能是否已通过物理选项启用，假定已定义mjModel\* m。x的类型是 :ref:`mjtEnableBit`。

.. _mjMAX:

mjMAX
~~~~~

.. code-block:: C

   #define mjMAX(a,b) (((a) > (b)) ? (a) : (b))

Return maximum value. To avoid repeated evaluation with mjtNum types, use the function :ref:`mju_max`.

返回最大值。为了避免在mjtNum类型中重复评估，可以使用函数:ref:`mju_max`。

.. _mjMIN:

mjMIN
~~~~~

.. code-block:: C

   #define mjMIN(a,b) (((a) < (b)) ? (a) : (b))

Return minimum value. To avoid repeated evaluation with mjtNum types, use the function :ref:`mju_min`.

返回最小值。为了避免在mjtNum类型中重复评估，可以使用函数:ref:`mju_min`。

.. _mjPLUGIN_LIB_INIT:

mjPLUGIN_LIB_INIT
~~~~~~~~~~~~~~~~~

.. code-block:: C

   #define mjPLUGIN_LIB_INIT                                                                 \
     static void _mjplugin_dllmain(void);                                                    \
     mjEXTERNC int __stdcall mjDLLMAIN(void* hinst, unsigned long reason, void* reserved) {  \
       if (reason == 1) {                                                                    \
         _mjplugin_dllmain();                                                                \
       }                                                                                     \
       return 1;                                                                             \
     }                                                                                       \
     static void _mjplugin_dllmain(void)

Register a plugin as a dynamic library. See :ref:`plugin registration<exRegistration>` for more details.

将插件注册为动态库。有关详细信息，请参阅 :ref:`plugin registration<exRegistration>`。