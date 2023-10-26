<template>
    <div id="header" class="ctextContent"><img src="../assets/imgs/edit.png"></div>
    <div id="footer" class="ctextContent">优秀的在线排版工具，免费、安全、无广告。</div>
    <div class="box">
        <div class="soft">
            <div class="s_textContent">
                <form method="POST" name="textForm">
                    <textarea ref="textRef" name="Content" rows="25" cols="20" id="thetextContent" class="t14"></textarea>
                </form>
            </div>
            <div class="btn-item">
                <input class="custom-btn" name="textContent" @click="removeHtmlTags()" title="清除文本中的HTML标签" type="button"
                    value="清除HTML" />
                <input class="custom-btn" name="textContent" @click="autoFormat()" title="系统自动排版" type="button"
                    value="自动排版" />
                <input class="custom-btn" title="仅用于删除文章内多余空行及空格" @click=removeBlankLines() value="消除空行" type="button"
                    name="textContent1" />
                <input class="custom-btn" name="textContent3" @click="AddLineBreak()" title="自动段间空行自动排版" type="button"
                    value="添加空行" />
                <input class="custom-btn" name="chang" @click="convertPunctuation()" title="将英文的标点转为中文标点" type="button"
                    value="标点英转中" />
                <input class="custom-btn" name="len" @click="chklen()" title="统计输入汉字数" type="button" value="统计字数" />

            </div>
            <div class="btn-item">
                <input class="custom-btn" id="jian2fan" name="jian2fan" @click="jian2fan()" title="简体转繁体" type="button"
                    value="简体转繁体" />
                <input class="custom-btn" id="fan2jian" name="fan2jian" @click="fan2jian()" title="繁体转简体" type="button"
                    value="繁体转简体" />
                <input class="custom-btn" name="cmdok" @click="copyTextToClipboard()" title="复制排版文章内容" type="button"
                    value="复制内容" />
                <input class="custom-btn" name="cmdReset" @click="clearText()" title="清空文章内容" type="button" value="清空内容" />
            </div>
        </div>
    </div>
    <div id="footer" class="ctextContent">
        请使用"Ctrl+V"粘贴,"Ctrl+C"复制
    </div>
</template>

<script setup>
import { ref } from 'vue'
import cocoMessage from 'coco-message'

const textRef = ref(null)

// 清除文本中的html标签
const removeHtmlTags = () => {
    let textContent = textRef.value.value
    textContent = textContent.replace(/<[^>].*?>/g, "");
    textContent = textContent.replace(/&nbsp;/g, "");
    textRef.value.value = textContent
}

// 一键排版
const autoFormat = () => {
    let textContent = textRef.value.value
    textContent = textContent.replace(/[\r\n]+/g, "\n").replace(/^\n|\n$|\s+$|[ ]+|\t+/mg, "");
    textContent = textContent.replace(/<\/?a[^>]*>/ig, "");
    textContent = textContent.replace(/<p>(&nbsp;|\s)*<\/p>/g, "");
    textContent = textContent.replace(/^\s*/gm, "    ");
    textContent = textContent.replace(/&nbsp;/g, "");
    textRef.value.value = textContent
}

// 清除空行与空格
const removeBlankLines = () => {
    let textContent = textRef.value.value
    textContent = textContent.replace(/ |　/ig, '');
    textContent = textContent.replace(/\r\n/ig, '\n');
    textContent = textContent.replace(/\n+/ig, '\n');
    textContent = textContent.replace(/\n\n/ig, '\n');
    textContent = textContent.replace(/\n\n/ig, '\n');
    textContent = textContent.replace(/\n\n/ig, '\n');
    textContent = textContent.replace(/\n/ig, '  \n');
    textContent = textContent.replace(/[ \t\r\n]+$/gm, '')
    textContent = textContent.replace("\n\n", "\n");
    textRef.value.value = textContent
}

// 给每个段落中间添加一行空白行
const AddLineBreak = () => {
    let textContent = textRef.value.value
    // 替换\r\n为\n
    textContent = textContent.replace(/\r\n/ig, "\n");
    // 替换一个或多个\n为两个\n
    textContent = textContent.replace(/\n+/g, "\n\n");
    // 去掉开头和结尾的空行
    textContent = textContent.replace(/^\n+|\n+$/g, "");
    textRef.value.value = textContent
}

// 英文标点符号转换为中文标点符号
const convertPunctuation = () => {
    // 定义一个对象，存储英文标点符号和对应的中文标点符号的映射关系
    var punctuationMap = {
        ',': '，',
        '......': '……',
        '。。。。。。': '……',
        '.': '。',
        '?': '？',
        '!': '！',
        ':': '：',
        ';': '；',
        '(': '（',
        ')': '）',
        '[': '【',
        ']': '】',
        '{': '｛',
        '}': '｝',
        '----': '——',
        '--': '——',

    };
    let textContent = textRef.value.value
    // 使用正则表达式，匹配字符串中的英文标点符号，并用replace方法替换成对应的中文标点符号
    var regex = /(\.{6,}|,{1,}|[.\?!:;"'(){}\[\]]|。{6,})/g;
    var result = textContent.replace(regex, function (match) {
        return punctuationMap[match] || match;
    });
    // 返回替换后的字符串
    textRef.value.value = result
}

// 复制文本到剪切板
const copyTextToClipboard = async () => {
    const Textarea = textRef.value.value;
    try {
        await navigator.clipboard.writeText(Textarea);
        cocoMessage.success('已成功复制文本到剪切板')
    } catch (err) {
        cocoMessage.error('无法复制文本到剪切板')
    }
}

// 清空文本
const clearText = () => {
    textRef.value.value = "";
}

// 统计文本长度
const chklen = () => {
    let strlen = textRef.value.value.length;
    cocoMessage.info(`当前文章长度，${strlen}个文字`)
}

// 将本地的简繁字典读取保存
const scText = ref('')
const tcText = ref('')

const loadDictionary = async () => {
    // 使用Fetch API加载并保存sc.txt的内容
    const scResponse = await fetch('/sc.txt');
    const scData = await scResponse.text();
    scText.value = scData;
    // console.log(scText.value)

    // 使用Fetch API加载并保存tc.txt的内容
    const tcResponse = await fetch('/tc.txt');
    const tcData = await tcResponse.text();
    tcText.value = tcData;
    // console.log(tcText.value)
};

// 加载字典数据
loadDictionary();
// console.log(scText.value, tcText.value)

const t2s = (str) => {
    var ret = "", i, len, idx;
    str = str || tcText.value;
    for (i = 0, len = str.length; i < len; i++) {
        idx = tcText.value.indexOf(str.charAt(i));
        ret += (idx === -1) ? str.charAt(i) : scText.value.charAt(idx);
    }
    return ret;
};

const s2t = (str) => {
    var ret = "", i, len, idx;
    str = str || scText.value;
    for (i = 0, len = str.length; i < len; i++) {
        idx = scText.value.indexOf(str.charAt(i));
        ret += (idx === -1) ? str.charAt(i) : tcText.value.charAt(idx);
    }
    return ret;
};

// 简体转繁体
const jian2fan = () => {
    let textContent = textRef.value.value
    if (textContent.trim() !== '') {
        var result = s2t(textContent)
        textRef.value.value = result
    }
}

// 繁体转简体
const fan2jian = () => {
    let textContent = textRef.value.value
    if (textContent.trim() !== '') {
        var result = t2s(textContent)
        textRef.value.value = result
    }
}

</script>

<style scoped>
* {
    padding: 0px;
    margin: 0px;
    box-sizing: border-box;
    -moz-box-sizing: border-box;
    -webkit-box-sizing: border-box;
}

textarea:focus-visible {
    outline: 1px dashed #d3eace;
}

textarea {
    font-family: 宋体;
    transition: outline-color 0.5s ease-in-out;
}

.ctextContent {
    margin: 0 auto;
    width: 952px;
}

.box {
    min-width: 952px;
}

.box .soft {
    width: 100%;
    height: auto;
    border: #d3eace 1px dashed;
    padding: 12px;
}

.t14 {
    font-size: 14px;
    width: 100%;
    height: 400px;
    border: #d3eace 1px dashed;
    background: #f3fff1;
}

.box .soft .s_textContent {
    width: 100%;
    text-align: center;
}

#footer {
    color: #737373;
    text-align: center;
    line-height: 18px;
    padding: 15px 0 18px 0;
}

.btn-item {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    gap: 14px;
    margin-top: 12px
}

.custom-btn {
    padding: 6px 12px;
    font-size: 14px;
    text-align: center;
    background-color: #3276b1;
    border: 1px solid #285e8e;
    color: #fff;
    line-height: 1.42857143;
    border-radius: 4px;
    cursor: pointer;
}

.custom-btn:active {
    background-color: #285e8e;
    border: 1px solid #285e8e;
    color: #fff;
}
</style>
