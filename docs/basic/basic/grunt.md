---
nav:
  title: 基本综述
  order: 1
group:
  title: 基本概念
  order: 1
title: Grunt
order: 3
---

# Grunt

Grunt 是基于 Node.js 的命令行工具。实际上它也是一个 **任务运行器**（Task Runner），可用于自动化构建、测试、生成文档的项目工具。

Grunt 通过 `grunt` 对象暴露所有方法和属性，并将此对象赋予 `module.exports` 函数，这些方法和属性都将传递到你的 Gruntfile、Grunt 插件或者 `task` 文件中。

所有的方法几乎都是在别处定义的，但是为了方便使用，也在 `grunt` 对象中做了定义。详细的解释和案例请参阅各个 [API](https://www.gruntjs.net/api/grunt) 单独的文档。

## Gruntfile

`Gruntfile.js` 或 `Gruntfile.coffee` 文件是有效的 JavaScript 或 CoffeeScript 文件，应当放在你的项目根目录中，和 `package.json` 文件在同一目录层级，并和项目源码一起加入源码管理器。

Gruntfile 由以下几部分构成：

- `"wrapper"` 函数
- 项目与任务配置
- 加载 Grunt 插件和任务
- 自定义任务

### 文件案例

在下面列出的这个 Gruntfile 中，`package.json` 文件中的项目元数据（metadata）被导入到 Grunt 配置中， `grunt-contrib-uglify` 插件中的 `uglify` 任务（task）被配置为压缩（minify）源码文件并依据上述元数据动态生成一个文件头注释。当在命令行中执行 `grunt` 命令时，`uglify` 任务将被默认执行。

```js
module.exports = function (grunt) {
  // 项目初始化配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n',
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js',
      },
    },
  });

  // 加载包含 "uglify" 任务的插件。
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认被执行的任务列表。
  grunt.registerTask('default', ['uglify']);
};
```

### 包装函数

每一份 Gruntfile （和 Grunt 插件）都遵循同样的格式，你所书写的 Grunt 代码必须放在此函数内：

```js
module.exports = function (grunt) {
  // Do grunt-related things in here
};
```

### 项目和任务配置

大部分的 Grunt 任务都依赖某些配置数据，这些数据被定义在一个 `object` 内，并传递给 `grunt.initConfig` 方法。

在下面的案例中，`grunt.file.readJSON('package.json')` 将存储在 `package.json` 文件中的 JSON 元数据引入到 `grunt config` 中。 由于 `<% %>` 模板字符串可以引用任意的配置属性，因此可以通过这种方式来指定诸如文件路径和文件列表类型的配置数据，从而减少一些重复的工作。

你可以在这个配置对象中（传递给 `initConfig()`方法的对象）存储任意的数据，只要它不与你任务配置所需的属性冲突，否则会被忽略。此外，由于这本身就是 JavaScript，你不仅限于使用 JSON；你可以在这里使用任意的有效的 JS 代码。如果有必要，你甚至可以以编程的方式生成配置。

与大多数 task 一样，`grunt-contrib-uglify` 插件中的 `uglify` 任务要求它的配置被指定在一个同名属性中。在这里有一个例子, 我们指定了一个 `banner` 选项(用于在文件顶部生成一个注释)，紧接着是一个单一的名为 `build` 的 `uglify` 目标，用于将一个 JS 文件压缩为一个目标文件。

```js
// Project configuration.
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n',
    },
    build: {
      src: 'src/<%= pkg.name %>.js',
      dest: 'build/<%= pkg.name %>.min.js',
    },
  },
});
```

### 加载 Grunt 插件和任务

像 concatenation、minification、grunt-contrib-uglify 和 linting 这些常用的任务（task）都已经以 Grunt 插件的形式被开发出来了。只要在 `package.json` 文件中被列为 `dependency`（依赖）的包，并通过 `npm install` 安装之后，都可以在 Gruntfile 中以简单命令的形式使用：

```js
// 加载能够提供"uglify"任务的插件。
grunt.loadNpmTasks('grunt-contrib-uglify');
```

注意：`grunt --help` 命令将列出所有可用的任务。

### 自定义任务

通过定义 `default` 任务，可以让 Grunt 默认执行一个或多个任务。在下面的这个案例中，执行 `grunt` 命令时如果不指定一个任务的话，将会执行 `uglify` 任务。这和执行 `grunt uglify` 或者 `grunt default` 的效果一样。`default` 任务列表数组中可以指定任意数目的任务（可以带参数）。

```js
// Default task(s).
grunt.registerTask('default', ['uglify']);
```

如果 Grunt 插件中的任务（task）不能满足你的项目需求，你还可以在 Gruntfile 中自定义任务（task）。例如，在下面的 Gruntfile 中自定义了一个 `default` 任务，并且他甚至不依赖任务配置：

```js
module.exports = function (grunt) {
  // A very basic default task.
  grunt.registerTask('default', 'Log some stuff.', function () {
    grunt.log.write('Logging some stuff...').ok();
  });
};
```

特定于项目的任务不必在 Gruntfile 中定义。他们可以定义在外部 `.js` 文件中，并通过 `grunt.loadTasks` 方法加载。

## 参考资料

- [📝 使用 Grunt 构建任务管理脚本](https://www.w3cplus.com/tools/writing-awesome-build-script-grunt.html)
- [📝 有关 Grunt - 自动化构建工具的奇技淫巧](https://segmentfault.com/a/1190000005029360)
