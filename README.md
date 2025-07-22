<html lang="zh-CN"><head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>农产品溯源查询系统</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#2E7D32', // 主色调：绿色，代表农产品、健康
                        secondary: '#FF9800', // 辅助色：橙色，代表活力、温暖
                        neutral: {
                            100: '#F5F5F5',
                            200: '#EEEEEE',
                            300: '#E0E0E0',
                            400: '#BDBDBD',
                            500: '#9E9E9E',
                            600: '#757575',
                            700: '#616161',
                            800: '#424242',
                            900: '#212121',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                    boxShadow: {
                        'custom': '0 4px 20px rgba(0, 0, 0, 0.08)',
                        'custom-hover': '0 8px 30px rgba(0, 0, 0, 0.12)',
                    }
                },
            }
        }
    </script>
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .timeline-line {
                @apply absolute left-5 top-6 h-full w-0.5 bg-neutral-200;
            }
            .timeline-dot {
                @apply absolute left-3 top-6 h-5 w-5 rounded-full border-4 border-white bg-primary;
            }
            .card-hover {
                @apply transition-all duration-300 hover:shadow-custom-hover hover:-translate-y-1;
            }
            .text-balance {
                text-wrap: balance;
            }
            .scrollbar-hide::-webkit-scrollbar {
                display: none;
            }
            .scrollbar-hide {
                -ms-overflow-style: none;
                scrollbar-width: none;
            }
            .upload-area {
                @apply relative border-2 border-dashed border-neutral-300 rounded-lg p-6 text-center cursor-pointer transition-colors hover:border-primary/50 hover:bg-primary/5;
            }
            .upload-area.active {
                @apply border-primary bg-primary/10;
            }
        }
    </style>
</head>
<body class="bg-neutral-100 font-sans text-neutral-800 min-h-screen flex flex-col">
    <!-- 导航栏 -->
    <header class="bg-white shadow-sm sticky top-0 z-50 transition-all duration-300">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
                <div class="flex items-center">
                    <a href="#" class="flex items-center">
                        <i class="fa fa-leaf text-primary text-2xl mr-2"></i>
                        <span class="font-bold text-xl text-primary">农溯通</span>
                    </a>
                    <nav class="hidden md:ml-10 md:flex space-x-8">
                        <a href="#" class="text-primary border-b-2 border-primary px-1 pt-1 font-medium">首页</a>
                        <a href="#" class="text-neutral-600 hover:text-primary px-1 pt-1 font-medium transition-colors">溯源查询</a>
                        <a href="#" class="text-neutral-600 hover:text-primary px-1 pt-1 font-medium transition-colors">企业入驻</a>
                        <a href="#" class="text-neutral-600 hover:text-primary px-1 pt-1 font-medium transition-colors">关于我们</a>
                    </nav>
                </div>
                <div class="flex items-center">
                    <button id="search-history-btn" class="hidden md:flex items-center px-4 py-2 border border-neutral-300 text-neutral-700 rounded-md hover:bg-neutral-50 transition-colors mr-4">
                        <i class="fa fa-history mr-2"></i>
                        <span>历史查询</span>
                    </button>
                    <button class="hidden md:flex items-center px-4 py-2 border border-primary text-primary rounded-md hover:bg-primary/5 transition-colors mr-4">
                        <i class="fa fa-search mr-2"></i>
                        <span>扫码查询</span>
                    </button>
                    <button class="md:hidden text-neutral-600 hover:text-primary transition-colors">
                        <i class="fa fa-bars text-xl"></i>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- 搜索区域 -->
    <section class="bg-gradient-to-br from-primary/90 to-primary py-12 md:py-16">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="max-w-3xl mx-auto text-center mb-8">
                <h1 class="text-[clamp(1.8rem,4vw,2.8rem)] font-bold text-white mb-4">农产品溯源查询</h1>
                <p class="text-white/80 text-lg mb-8">
                    输入溯源码、扫描二维码或上传图片，查询农产品的完整生产、物流和销售信息
                </p>
                
                <!-- 搜索方式切换 -->
                <div class="flex justify-center mb-6">
                    <button id="code-search-btn" class="px-6 py-2 bg-white text-primary font-medium rounded-l-lg shadow-md">
                        <i class="fa fa-barcode mr-2"></i>溯源码查询
                    </button>
                    <button id="image-search-btn" class="px-6 py-2 bg-neutral-200 text-neutral-700 font-medium rounded-r-lg shadow-md hover:bg-neutral-300 transition-colors">
                        <i class="fa fa-image mr-2"></i>图片查询
                    </button>
                </div>
                
                <!-- 溯源码查询表单 -->
                <div id="code-search-form" class="bg-white rounded-xl shadow-lg p-4 md:p-6">
                    <div class="flex flex-col md:flex-row gap-4">
                        <div class="relative flex-grow">
                            <input type="text" placeholder="请输入溯源码" id="traceability-code" class="w-full px-4 py-3 rounded-lg border border-neutral-300 focus:outline-none focus:ring-2 focus:ring-primary/50 focus:border-primary">
                            <div class="absolute right-3 top-1/2 -translate-y-1/2 flex items-center space-x-2">
                                <button class="text-neutral-400 hover:text-neutral-600 transition-colors">
                                    <i class="fa fa-barcode"></i>
                                </button>
                                <button class="text-neutral-400 hover:text-neutral-600 transition-colors">
                                    <i class="fa fa-keyboard-o"></i>
                                </button>
                            </div>
                        </div>
                        <button class="bg-primary hover:bg-primary/90 text-white font-medium py-3 px-6 rounded-lg transition-colors">
                            <i class="fa fa-search mr-2"></i>
                            <span>查询</span>
                        </button>
                    </div>
                    <div class="mt-4 text-center">
                        <p class="text-sm text-neutral-500">
                            也可以使用
                            <button class="text-primary hover:text-primary/80 font-medium">
                                <i class="fa fa-qrcode mr-1"></i>扫码查询
                            </button>
                        </p>
                    </div>
                </div>
                
                <!-- 图片查询表单 -->
                <div id="image-search-form" class="bg-white rounded-xl shadow-lg p-4 md:p-6 hidden">
                    <div class="upload-area" id="upload-area">
                        <input type="file" id="image-upload" accept="image/*" class="hidden">
                        <div id="upload-icon" class="mb-3">
                            <i class="fa fa-cloud-upload text-primary text-4xl"></i>
                        </div>
                        <h3 class="font-medium text-neutral-800 mb-2">拖拽图片到此处上传</h3>
                        <p class="text-neutral-500 text-sm mb-4">或者点击选择图片文件，系统将自动识别农产品类型</p>
                        <button id="select-image-btn" class="bg-primary hover:bg-primary/90 text-white font-medium py-2 px-4 rounded-lg transition-colors">
                            <i class="fa fa-picture-o mr-2"></i>选择图片
                        </button>
                    </div>
                    
                    <!-- 图片预览区域 -->
                    <div id="image-preview-container" class="mt-6 hidden">
                        <h3 class="font-medium text-neutral-800 mb-3">图片预览</h3>
                        <div class="relative">
                            <img id="image-preview" src="" alt="上传的图片" class="max-w-full h-auto rounded-lg border border-neutral-200">
                            <button id="remove-image-btn" class="absolute top-2 right-2 bg-white/80 hover:bg-white text-neutral-700 rounded-full w-8 h-8 flex items-center justify-center transition-colors">
                                <i class="fa fa-times"></i>
                            </button>
                        </div>
                        <button id="search-by-image-btn" class="mt-4 w-full bg-primary hover:bg-primary/90 text-white font-medium py-3 px-6 rounded-lg transition-colors">
                            <i class="fa fa-search mr-2"></i>
                            <span>通过图片查询</span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- 主要内容 -->
    <main class="flex-grow container mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <!-- 历史查询记录 -->
        <div id="search-history" class="bg-white rounded-xl shadow-custom p-6 mb-8 hidden">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-xl font-bold text-neutral-800">历史查询记录</h2>
                <button id="close-history-btn" class="text-neutral-500 hover:text-neutral-700 transition-colors">
                    <i class="fa fa-times"></i>
                </button>
            </div>
            <div class="overflow-x-auto scrollbar-hide">
                <table class="min-w-full">
                    <thead>
                        <tr class="border-b border-neutral-200">
                            <th class="text-left py-3 px-4 text-neutral-600 font-medium">产品名称</th>
                            <th class="text-left py-3 px-4 text-neutral-600 font-medium">溯源码</th>
                            <th class="text-left py-3 px-4 text-neutral-600 font-medium">查询时间</th>
                            <th class="text-left py-3 px-4 text-neutral-600 font-medium">操作</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="border-b border-neutral-200 hover:bg-neutral-50 transition-colors">
                            <td class="py-3 px-4">
                                <div class="flex items-center">
                                    <img src="https://picsum.photos/seed/apple1/50/50" alt="有机红富士苹果" class="w-10 h-10 rounded-lg object-cover mr-3">
                                    <span class="font-medium">有机红富士苹果</span>
                                </div>
                            </td>
                            <td class="py-3 px-4 text-neutral-600">AGT20250722001</td>
                            <td class="py-3 px-4 text-neutral-500">2025-07-22 14:30:25</td>
                            <td class="py-3 px-4">
                                <button class="text-primary hover:text-primary/80 transition-colors">
                                    <i class="fa fa-search mr-1"></i>查看
                                </button>
                            </td>
                        </tr>
                        <tr class="border-b border-neutral-200 hover:bg-neutral-50 transition-colors">
                            <td class="py-3 px-4">
                                <div class="flex items-center">
                                    <img src="https://picsum.photos/seed/strawberry/50/50" alt="有机草莓" class="w-10 h-10 rounded-lg object-cover mr-3">
                                    <span class="font-medium">有机草莓</span>
                                </div>
                            </td>
                            <td class="py-3 px-4 text-neutral-600">AGT20250720005</td>
                            <td class="py-3 px-4 text-neutral-500">2025-07-20 09:15:40</td>
                            <td class="py-3 px-4">
                                <button class="text-primary hover:text-primary/80 transition-colors">
                                    <i class="fa fa-search mr-1"></i>查看
                                </button>
                            </td>
                        </tr>
                        <tr class="border-b border-neutral-200 hover:bg-neutral-50 transition-colors">
                            <td class="py-3 px-4">
                                <div class="flex items-center">
                                    <img src="https://picsum.photos/seed/tomato/50/50" alt="有机樱桃番茄" class="w-10 h-10 rounded-lg object-cover mr-3">
                                    <span class="font-medium">有机樱桃番茄</span>
                                </div>
                            </td>
                            <td class="py-3 px-4 text-neutral-600">AGT20250718012</td>
                            <td class="py-3 px-4 text-neutral-500">2025-07-18 16:45:12</td>
                            <td class="py-3 px-4">
                                <button class="text-primary hover:text-primary/80 transition-colors">
                                    <i class="fa fa-search mr-1"></i>查看
                                </button>
                            </td>
                        </tr>
                        <tr class="hover:bg-neutral-50 transition-colors">
                            <td class="py-3 px-4">
                                <div class="flex items-center">
                                    <img src="https://picsum.photos/seed/orange/50/50" alt="赣南脐橙" class="w-10 h-10 rounded-lg object-cover mr-3">
                                    <span class="font-medium">赣南脐橙</span>
                                </div>
                            </td>
                            <td class="py-3 px-4 text-neutral-600">AGT20250715023</td>
                            <td class="py-3 px-4 text-neutral-500">2025-07-15 11:20:38</td>
                            <td class="py-3 px-4">
                                <button class="text-primary hover:text-primary/80 transition-colors">
                                    <i class="fa fa-search mr-1"></i>查看
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="mt-6 flex justify-between items-center">
                <p class="text-sm text-neutral-500">显示最近 4 条查询记录</p>
                <button class="text-primary hover:text-primary/80 font-medium transition-colors">
                    查看全部历史记录
                </button>
            </div>
        </div>

        <!-- 查询结果头部 -->
        <div class="bg-white rounded-xl shadow-custom p-6 mb-8">
            <div class="flex flex-col md:flex-row md:items-center md:justify-between">
                <div>
                    <h1 class="text-[clamp(1.5rem,3vw,2.5rem)] font-bold text-neutral-800 mb-2">农产品溯源详情</h1>
                    <p class="text-neutral-500 mb-4">追溯码: <span class="font-medium text-neutral-700">AGT20250722001</span></p>
                    <div class="flex flex-wrap gap-3">
                        <span class="px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">已验证</span>
                        <span class="px-3 py-1 bg-neutral-100 text-neutral-600 rounded-full text-sm font-medium">绿色食品</span>
                        <span class="px-3 py-1 bg-neutral-100 text-neutral-600 rounded-full text-sm font-medium">非转基因</span>
                    </div>
                </div>
                <div class="mt-6 md:mt-0">
                    <div class="bg-neutral-100 p-4 rounded-lg border border-neutral-200">
                        <div class="flex items-center justify-between mb-3">
                            <span class="text-neutral-600 font-medium">产品名称</span>
                            <span class="text-primary font-bold text-lg">有机红富士苹果</span>
                        </div>
                        <div class="flex items-center justify-between">
                            <span class="text-neutral-600 font-medium">批次号</span>
                            <span class="text-neutral-800 font-medium">AP20250715</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 溯源信息卡片 -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
            <!-- 生产方信息 -->
            <div class="bg-white rounded-xl shadow-custom p-6 card-hover">
                <div class="flex items-center mb-4">
                    <div class="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center mr-4">
                        <i class="fa fa-tree text-primary text-xl"></i>
                    </div>
                    <h2 class="text-xl font-bold text-neutral-800">生产方信息</h2>
                </div>
                <div class="border-t border-neutral-200 pt-4">
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">种植基地</h3>
                        <p class="font-medium text-neutral-800">绿源有机农场</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">产地</h3>
                        <p class="font-medium text-neutral-800">山东省烟台市栖霞市</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">种植日期</h3>
                        <p class="font-medium text-neutral-800">2025年04月10日</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">采摘日期</h3>
                        <p class="font-medium text-neutral-800">2025年09月15日</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">生产标准</h3>
                        <p class="font-medium text-neutral-800">GB/T 19630-2019 有机产品</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">生产过程</h3>
                        <div class="space-y-2">
                            <div class="flex items-center">
                                <i class="fa fa-check-circle text-primary mr-2"></i>
                                <span class="text-neutral-700">使用有机肥料</span>
                            </div>
                            <div class="flex items-center">
                                <i class="fa fa-check-circle text-primary mr-2"></i>
                                <span class="text-neutral-700">物理防虫技术</span>
                            </div>
                            <div class="flex items-center">
                                <i class="fa fa-check-circle text-primary mr-2"></i>
                                <span class="text-neutral-700">人工疏果</span>
                            </div>
                        </div>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">生产方资质</h3>
                        <div class="grid grid-cols-2 gap-2">
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-primary mr-2"></i>
                                <span class="text-sm text-neutral-700">有机认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-primary mr-2"></i>
                                <span class="text-sm text-neutral-700">绿色食品认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-primary mr-2"></i>
                                <span class="text-sm text-neutral-700">质量管理体系认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-primary mr-2"></i>
                                <span class="text-sm text-neutral-700">环境管理体系认证</span>
                            </div>
                        </div>
                    </div>
                    <div>
                        <h3 class="text-neutral-600 mb-2">质检报告</h3>
                        <a href="#" class="text-primary hover:text-primary/80 font-medium flex items-center">
                            <i class="fa fa-file-text-o mr-2"></i>
                            <span>查看报告</span>
                        </a>
                    </div>
                </div>
            </div>

            <!-- 物流方信息 -->
            <div class="bg-white rounded-xl shadow-custom p-6 card-hover">
                <div class="flex items-center mb-4">
                    <div class="w-12 h-12 rounded-full bg-secondary/10 flex items-center justify-center mr-4">
                        <i class="fa fa-truck text-secondary text-xl"></i>
                    </div>
                    <h2 class="text-xl font-bold text-neutral-800">物流方信息</h2>
                </div>
                <div class="border-t border-neutral-200 pt-4">
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">物流公司</h3>
                        <p class="font-medium text-neutral-800">鲜速达冷链物流</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">运输方式</h3>
                        <p class="font-medium text-neutral-800">冷藏车运输</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">起运地</h3>
                        <p class="font-medium text-neutral-800">山东省烟台市栖霞市</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">目的地</h3>
                        <p class="font-medium text-neutral-800">上海市浦东新区</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">发货日期</h3>
                        <p class="font-medium text-neutral-800">2025年09月16日</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">到货日期</h3>
                        <p class="font-medium text-neutral-800">2025年09月18日</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">物流追踪</h3>
                        <div class="relative pl-10">
                            <div class="timeline-line"></div>
                            <div class="timeline-dot"></div>
                            <div class="mb-6">
                                <p class="font-medium text-neutral-800">已签收</p>
                                <p class="text-sm text-neutral-500">2025-09-18 10:30:25</p>
                                <p class="text-sm text-neutral-600">上海市浦东新区配送点</p>
                            </div>
                            <div class="timeline-dot top-32"></div>
                            <div class="mb-6 ml-1">
                                <p class="font-medium text-neutral-800">正在派送</p>
                                <p class="text-sm text-neutral-500">2025-09-18 08:15:42</p>
                                <p class="text-sm text-neutral-600">上海市浦东新区配送点</p>
                            </div>
                            <div class="timeline-dot top-58"></div>
                            <div class="ml-1">
                                <p class="font-medium text-neutral-800">已到达</p>
                                <p class="text-sm text-neutral-500">2025-09-18 06:20:10</p>
                                <p class="text-sm text-neutral-600">上海市浦东新区配送中心</p>
                            </div>
                        </div>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">物流方资质</h3>
                        <div class="grid grid-cols-2 gap-2">
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-secondary mr-2"></i>
                                <span class="text-sm text-neutral-700">道路运输经营许可证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-secondary mr-2"></i>
                                <span class="text-sm text-neutral-700">冷链物流资质认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-secondary mr-2"></i>
                                <span class="text-sm text-neutral-700">ISO 9001质量管理认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-secondary mr-2"></i>
                                <span class="text-sm text-neutral-700">食品运输安全认证</span>
                            </div>
                        </div>
                    </div>
                    <div>
                        <a href="#" class="text-secondary hover:text-secondary/80 font-medium flex items-center">
                            <i class="fa fa-map-marker mr-2"></i>
                            <span>查看完整物流轨迹</span>
                        </a>
                    </div>
                </div>
            </div>

            <!-- 销售方信息 -->
            <div class="bg-white rounded-xl shadow-custom p-6 card-hover">
                <div class="flex items-center mb-4">
                    <div class="w-12 h-12 rounded-full bg-blue-100 flex items-center justify-center mr-4">
                        <i class="fa fa-shopping-bag text-blue-600 text-xl"></i>
                    </div>
                    <h2 class="text-xl font-bold text-neutral-800">销售方信息</h2>
                </div>
                <div class="border-t border-neutral-200 pt-4">
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">销售平台</h3>
                        <p class="font-medium text-neutral-800">鲜享生鲜超市</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">门店地址</h3>
                        <p class="font-medium text-neutral-800">上海市浦东新区张江高科技园区博云路2号</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">上架日期</h3>
                        <p class="font-medium text-neutral-800">2025年09月18日</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">保质期</h3>
                        <p class="font-medium text-neutral-800">15天</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">储存条件</h3>
                        <p class="font-medium text-neutral-800">冷藏（2-8℃）</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">产品规格</h3>
                        <p class="font-medium text-neutral-800">500g/盒，约5-6个</p>
                    </div>
                    <div class="mb-4">
                        <h3 class="text-neutral-600 mb-2">销售方资质</h3>
                        <div class="grid grid-cols-2 gap-2">
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-blue-600 mr-2"></i>
                                <span class="text-sm text-neutral-700">食品经营许可证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-blue-600 mr-2"></i>
                                <span class="text-sm text-neutral-700">营业执照</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-blue-600 mr-2"></i>
                                <span class="text-sm text-neutral-700">诚信经营认证</span>
                            </div>
                            <div class="bg-neutral-50 p-2 rounded flex items-center">
                                <i class="fa fa-certificate text-blue-600 mr-2"></i>
                                <span class="text-sm text-neutral-700">溯源销售认证</span>
                            </div>
                        </div>
                    </div>
                    <div class="mb-6">
                        <h3 class="text-neutral-600 mb-2">产品价格</h3>
                        <div class="flex items-baseline">
                            <span class="text-primary font-bold text-2xl">¥29.90</span>
                            <span class="text-neutral-500 line-through ml-2">¥39.90</span>
                            <span class="ml-2 px-2 py-0.5 bg-red-100 text-red-600 text-xs rounded-full">限时特惠</span>
                        </div>
                    </div>
                    <button class="w-full bg-primary hover:bg-primary/90 text-white font-medium py-3 px-4 rounded-lg transition-colors flex items-center justify-center">
                        <i class="fa fa-shopping-cart mr-2"></i>
                        <span>立即购买</span>
                    </button>
                </div>
            </div>
        </div>

        <!-- 产品图片和详细描述 -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8 mb-8">
            <div class="lg:col-span-2">
                <div class="bg-white rounded-xl shadow-custom p-6">
                    <h2 class="text-xl font-bold text-neutral-800 mb-4">产品详情</h2>
                    <div class="grid grid-cols-2 sm:grid-cols-4 gap-4 mb-6">
                        <div class="aspect-square rounded-lg overflow-hidden group cursor-pointer">
                            <img src="https://p3-flow-imagex-sign.byteimg.com/tos-cn-i-a9rns2rl98/rc/pc/code_assistant/f7e17d32fd80429486ef7e81821a5ec9~tplv-a9rns2rl98-image.image?rk3s=8e244e95&amp;rrcfp=2609e108&amp;x-expires=1753770290&amp;x-signature=xSAp%2Bq1o%2F3mkXoP1GZ%2BigjO2908%3D" alt="有机红富士苹果正面图" class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110">
                        </div>
                        <div class="aspect-square rounded-lg overflow-hidden group cursor-pointer">
                            <img src="https://p3-flow-imagex-sign.byteimg.com/tos-cn-i-a9rns2rl98/rc/pc/code_assistant/b4362880689f4187b216a6a9e5f8094e~tplv-a9rns2rl98-image.image?rk3s=8e244e95&amp;rrcfp=2609e108&amp;x-expires=1753770305&amp;x-signature=xmo3vFA3j1D2DNYK61Eh%2BP1CuIk%3D" alt="有机红富士苹果侧面图" class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110">
                        </div>
                        <div class="aspect-square rounded-lg overflow-hidden group cursor-pointer">
                            <img src="https://p9-flow-imagex-sign.byteimg.com/tos-cn-i-a9rns2rl98/rc/pc/code_assistant/064f0194d08d46f5bc6d9e143aa2c027~tplv-a9rns2rl98-image.image?rk3s=8e244e95&amp;rrcfp=2609e108&amp;x-expires=1753770318&amp;x-signature=5%2FTyyYNk9elQzPMatAZ2y%2Beyg98%3D" alt="有机红富士苹果包装图" class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110">
                        </div>
                        <div class="aspect-square rounded-lg overflow-hidden group cursor-pointer">
                            <img src="https://p9-flow-imagex-sign.byteimg.com/tos-cn-i-a9rns2rl98/rc/pc/code_assistant/bda45dded7574423b27fea512cd71302~tplv-a9rns2rl98-image.image?rk3s=8e244e95&amp;rrcfp=2609e108&amp;x-expires=1753770332&amp;x-signature=h%2FAMJNagJpg9JV%2BGB6fSI7yGQzs%3D" alt="有机红富士苹果种植环境图" class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110">
                        </div>
                    </div>
                    <div class="prose max-w-none">
                        <h3 class="text-lg font-semibold text-neutral-800 mb-2">产品介绍</h3>
                        <p class="text-neutral-700 mb-4 text-balance">
                            我们的有机红富士苹果产自山东烟台栖霞，这里拥有得天独厚的地理环境和气候条件，
                            全年光照充足，昼夜温差大，造就了苹果色泽鲜艳、果肉细密、汁多味甜的优良品质。
                        </p>
                        <p class="text-neutral-700 mb-4 text-balance">
                            该产品严格按照有机标准种植，全程不使用化肥、农药、生长调节剂等化学合成物质，
                            采用传统农耕方式与现代科技相结合的管理模式，确保每一个苹果都是纯天然、无污染的健康食品。
                        </p>
                        <h3 class="text-lg font-semibold text-neutral-800 mb-2">营养价值</h3>
                        <ul class="list-disc pl-5 text-neutral-700 mb-4 space-y-1">
                            <li>富含维生素C、维生素E和纤维素，有助于增强免疫力</li>
                            <li>含有丰富的苹果酸和柠檬酸，促进消化</li>
                            <li>低热量、高纤维，是健康减肥的理想选择</li>
                            <li>不含人工添加剂和防腐剂，安全放心</li>
                        </ul>
                        <h3 class="text-lg font-semibold text-neutral-800 mb-2">食用建议</h3>
                        <ul class="list-disc pl-5 text-neutral-700 space-y-1">
                            <li>建议生食，口感清甜脆爽</li>
                            <li>可制作苹果沙拉、苹果汁或烘焙食品</li>
                            <li>储存于阴凉干燥处，冷藏保存口感更佳</li>
                            <li>食用前请用清水冲洗干净</li>
                        </ul>
                    </div>
                </div>
            </div>
            <div>
                <div class="bg-white rounded-xl shadow-custom p-6">
                    <h2 class="text-xl font-bold text-neutral-800 mb-4">质量检测</h2>
                    <div class="space-y-4 mb-6">
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-neutral-600">农药残留检测</span>
                                <span class="text-green-600 font-medium">未检出</span>
                            </div>
                            <div class="w-full bg-neutral-200 rounded-full h-2.5">
                                <div class="bg-green-500 h-2.5 rounded-full" style="width: 0%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-neutral-600">重金属检测</span>
                                <span class="text-green-600 font-medium">符合标准</span>
                            </div>
                            <div class="w-full bg-neutral-200 rounded-full h-2.5">
                                <div class="bg-green-500 h-2.5 rounded-full" style="width: 100%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-neutral-600">微生物检测</span>
                                <span class="text-green-600 font-medium">合格</span>
                            </div>
                            <div class="w-full bg-neutral-200 rounded-full h-2.5">
                                <div class="bg-green-500 h-2.5 rounded-full" style="width: 100%"></div>
                            </div>
                        </div>
                        <div>
                            <div class="flex justify-between mb-1">
                                <span class="text-neutral-600">糖度检测</span>
                                <span class="text-primary font-medium">16.5°Bx</span>
                            </div>
                            <div class="w-full bg-neutral-200 rounded-full h-2.5">
                                <div class="bg-primary h-2.5 rounded-full" style="width: 85%"></div>
                            </div>
                        </div>
                    </div>
                    <div class="border-t border-neutral-200 pt-4">
                        <h3 class="text-lg font-semibold text-neutral-800 mb-3">认证信息</h3>
                        <div class="grid grid-cols-2 gap-3">
                            <div class="bg-neutral-50 p-3 rounded-lg flex items-center">
                                <img src="https://picsum.photos/seed/organic/50/50" alt="有机认证标志" class="w-8 h-8 mr-2">
                                <span class="text-sm text-neutral-700">有机认证</span>
                            </div>
                            <div class="bg-neutral-50 p-3 rounded-lg flex items-center">
                                <img src="https://picsum.photos/seed/green/50/50" alt="绿色食品标志" class="w-8 h-8 mr-2">
                                <span class="text-sm text-neutral-700">绿色食品</span>
                            </div>
                            <div class="bg-neutral-50 p-3 rounded-lg flex items-center">
                                <img src="https://picsum.photos/seed/quality/50/50" alt="质量安全标志" class="w-8 h-8 mr-2">
                                <span class="text-sm text-neutral-700">质量安全</span>
                            </div>
                            <div class="bg-neutral-50 p-3 rounded-lg flex items-center">
                                <img src="https://picsum.photos/seed/origin/50/50" alt="原产地保护标志" class="w-8 h-8 mr-2">
                                <span class="text-sm text-neutral-700">原产地保护</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 推荐产品 -->
        <div class="bg-white rounded-xl shadow-custom p-6 mb-8">
            <h2 class="text-xl font-bold text-neutral-800 mb-6">推荐产品</h2>
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
                <div class="rounded-lg overflow-hidden border border-neutral-200 card-hover">
                    <div class="aspect-square relative">
                        <img src="https://picsum.photos/seed/pear/400/400" alt="有机雪花梨" class="w-full h-full object-cover">
                        <div class="absolute top-2 right-2 bg-primary text-white text-xs font-bold px-2 py-1 rounded">有机</div>
                    </div>
                    <div class="p-4">
                        <h3 class="font-medium text-neutral-800 mb-1">有机雪花梨</h3>
                        <p class="text-neutral-500 text-sm mb-3">河北赵县特产</p>
                        <div class="flex justify-between items-center">
                            <span class="text-primary font-bold">¥19.90</span>
                            <button class="w-9 h-9 rounded-full bg-primary/10 flex items-center justify-center text-primary hover:bg-primary hover:text-white transition-colors">
                                <i class="fa fa-shopping-cart"></i>
                            </button>
                        </div>
                    </div>
                </div>
                <div class="rounded-lg overflow-hidden border border-neutral-200 card-hover">
                    <div class="aspect-square relative">
                        <img src="https://picsum.photos/seed/strawberry/400/400" alt="有机草莓" class="w-full h-full object-cover">
                        <div class="absolute top-2 right-2 bg-red-500 text-white text-xs font-bold px-2 py-1 rounded">新鲜直达</div>
                    </div>
                    <div class="p-4">
                        <h3 class="font-medium text-neutral-800 mb-1">有机草莓</h3>
                        <p class="text-neutral-500 text-sm mb-3">丹东九九草莓</p>
                        <div class="flex justify-between items-center">
                            <span class="text-primary font-bold">¥39.90</span>
                            <button class="w-9 h-9 rounded-full bg-primary/10 flex items-center justify-center text-primary hover:bg-primary hover:text-white transition-colors">
                                <i class="fa fa-shopping-cart"></i>
                            </button>
                        </div>
                    </div>
                </div>
                <div class="rounded-lg overflow-hidden border border-neutral-200 card-hover">
                    <div class="aspect-square relative">
                        <img src="https://picsum.photos/seed/tomato/400/400" alt="有机樱桃番茄" class="w-full h-full object-cover">
                        <div class="absolute top-2 right-2 bg-primary text-white text-xs font-bold px-2 py-1 rounded">有机</div>
                    </div>
                    <div class="p-4">
                        <h3 class="font-medium text-neutral-800 mb-1">有机樱桃番茄</h3>
                        <p class="text-neutral-500 text-sm mb-3">酸甜可口</p>
                        <div class="flex justify-between items-center">
                            <span class="text-primary font-bold">¥15.90</span>
                            <button class="w-9 h-9 rounded-full bg-primary/10 flex items-center justify-center text-primary hover:bg-primary hover:text-white transition-colors">
                                <i class="fa fa-shopping-cart"></i>
                            </button>
                        </div>
                    </div>
                </div>
                <div class="rounded-lg overflow-hidden border border-neutral-200 card-hover">
                    <div class="aspect-square relative">
                        <img src="https://picsum.photos/seed/orange/400/400" alt="赣南脐橙" class="w-full h-full object-cover">
                        <div class="absolute top-2 right-2 bg-secondary text-white text-xs font-bold px-2 py-1 rounded">特惠</div>
                    </div>
                    <div class="p-4">
                        <h3 class="font-medium text-neutral-800 mb-1">赣南脐橙</h3>
                        <p class="text-neutral-500 text-sm mb-3">江西特产</p>
                        <div class="flex justify-between items-center">
                            <span class="text-primary font-bold">¥25.90</span>
                            <button class="w-9 h-9 rounded-full bg-primary/10 flex items-center justify-center text-primary hover:bg-primary hover:text-white transition-colors">
                                <i class="fa fa-shopping-cart"></i>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 用户评价 -->
        <div class="bg-white rounded-xl shadow-custom p-6 mb-8">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-xl font-bold text-neutral-800">用户评价</h2>
                <div class="flex items-center">
                    <div class="flex mr-2">
                        <i class="fa fa-star text-yellow-400"></i>
                        <i class="fa fa-star text-yellow-400"></i>
                        <i class="fa fa-star text-yellow-400"></i>
                        <i class="fa fa-star text-yellow-400"></i>
                        <i class="fa fa-star-half-o text-yellow-400"></i>
                    </div>
                    <span class="text-neutral-600 font-medium">4.8 (126条评价)</span>
                </div>
            </div>
            <div class="space-y-6">
                <div class="border-b border-neutral-200 pb-6">
                    <div class="flex items-center mb-3">
                        <img src="https://picsum.photos/seed/user1/100/100" alt="用户头像" class="w-10 h-10 rounded-full object-cover mr-3">
                        <div>
                            <h4 class="font-medium text-neutral-800">李**</h4>
                            <div class="flex text-yellow-400 text-sm">
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                            </div>
                        </div>
                        <span class="ml-auto text-sm text-neutral-500">2025-09-20</span>
                    </div>
                    <p class="text-neutral-700 mb-3">苹果非常新鲜，口感清甜脆爽，汁水丰富，包装也很好，没有一个磕碰的。能查到详细的生产信息，吃着很放心，以后会经常购买。</p>
                    <div class="grid grid-cols-3 gap-2 max-w-xs">
                        <img src="https://picsum.photos/seed/review1/200/200" alt="用户上传的苹果图片1" class="rounded-lg">
                        <img src="https://picsum.photos/seed/review2/200/200" alt="用户上传的苹果图片2" class="rounded-lg">
                    </div>
                </div>
                <div class="border-b border-neutral-200 pb-6">
                    <div class="flex items-center mb-3">
                        <img src="https://picsum.photos/seed/user2/100/100" alt="用户头像" class="w-10 h-10 rounded-full object-cover mr-3">
                        <div>
                            <h4 class="font-medium text-neutral-800">王**</h4>
                            <div class="flex text-yellow-400 text-sm">
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star-o"></i>
                            </div>
                        </div>
                        <span class="ml-auto text-sm text-neutral-500">2025-09-19</span>
                    </div>
                    <p class="text-neutral-700">苹果个头均匀，色泽鲜艳，味道也不错，就是稍微有点贵。不过能看到整个生产和运输过程，感觉物有所值。物流也很快，包装严实，没有损坏。</p>
                </div>
                <div>
                    <div class="flex items-center mb-3">
                        <img src="https://picsum.photos/seed/user3/100/100" alt="用户头像" class="w-10 h-10 rounded-full object-cover mr-3">
                        <div>
                            <h4 class="font-medium text-neutral-800">张**</h4>
                            <div class="flex text-yellow-400 text-sm">
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star"></i>
                                <i class="fa fa-star-half-o"></i>
                            </div>
                        </div>
                        <span class="ml-auto text-sm text-neutral-500">2025-09-18</span>
                    </div>
                    <p class="text-neutral-700 mb-3">这是我吃过的最好吃的苹果之一，甜度很高，果肉很脆，而且很新鲜。可以查到是哪里种植的，什么时候采摘的，让人很放心。已经是第二次购买了，还会继续回购。</p>
                    <div class="grid grid-cols-3 gap-2 max-w-xs">
                        <img src="https://picsum.photos/seed/review3/200/200" alt="用户上传的苹果图片" class="rounded-lg">
                    </div>
                </div>
            </div>
            <button class="mt-6 w-full py-2 border border-neutral-300 rounded-lg text-neutral-700 hover:bg-neutral-50 transition-colors">
                查看全部评价
            </button>
        </div>

        <!-- 溯源二维码 -->
        <div class="bg-white rounded-xl shadow-custom p-6">
            <div class="flex flex-col md:flex-row md:items-center justify-between">
                <div class="mb-6 md:mb-0">
                    <h2 class="text-xl font-bold text-neutral-800 mb-2">扫码查看完整溯源信息</h2>
                    <p class="text-neutral-600 max-w-md">
                        您可以使用手机扫描二维码，随时随地查看该产品的完整溯源信息，包括生产过程视频、更多检测报告等详细内容。
                    </p>
                </div>
                <div class="flex flex-col items-center">
                    <div class="bg-white p-4 rounded-lg shadow-md mb-3">
                        <img src="https://picsum.photos/seed/qrcode/200/200" alt="产品溯源二维码" class="w-40 h-40">
                    </div>
                    <p class="text-sm text-neutral-600">微信/支付宝扫码查看</p>
                </div>
            </div>
        </div>
    </main>

    <!-- 页脚 -->
    <footer class="bg-neutral-800 text-white pt-12 pb-6">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-8 mb-8">
                <div>
                    <div class="flex items-center mb-4">
                        <i class="fa fa-leaf text-primary text-2xl mr-2"></i>
                        <span class="font-bold text-xl">农溯通</span>
                    </div>
                    <p class="text-neutral-400 mb-4">
                        农溯通是一家专注于农产品溯源的平台，致力于为消费者提供安全、透明、可信赖的农产品信息服务。
                    </p>
                    <div class="flex space-x-4">
                        <a href="#" class="text-neutral-400 hover:text-white transition-colors">
                            <i class="fa fa-weibo text-xl"></i>
                        </a>
                        <a href="#" class="text-neutral-400 hover:text-white transition-colors">
                            <i class="fa fa-wechat text-xl"></i>
                        </a>
                        <a href="#" class="text-neutral-400 hover:text-white transition-colors">
                            <i class="fa fa-instagram text-xl"></i>
                        </a>
                        </div></div></div></div></footer></body></html>
