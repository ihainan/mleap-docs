# MLeap Documentation Development | MLeap 文档开发

We use [GitBook](https://www.gitbook.com/) to write our documentation.

我们使用 [GitBook](https://www.gitbook.com/) 来编写文档。

## Install | 按摩证监会

Install the command line tools.

安装命令行工具：

```
npm install -g gitbook-cli
```

Install `ebook-convert` to publish the PDF.

安装 `ebook-convert` 来发布 PDF 文件。


```
brew cask install calibre
```

## Local Development Server | 本地开发服务器

Start a local server to see the documents.

启动一个本地服务器来阅读文档。

```
gitbook serve
```

## Publish PDF | 发布 PDF

```
gitbook pdf
```
