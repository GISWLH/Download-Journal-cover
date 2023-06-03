# Nature封面合集下载

## 引言

![1685793222253](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/1685793222253.png)

nature是最顶级的期刊之一，其期刊封面具有丰富的内涵与美感，非常值得学习。

然而，目前没有很系统的期刊封面整理，有的话往往也是收费或者难以获取。

为了方便快速获取nature及其子刊封面，小编提供了一套期刊封面的选定爬取方法及教程。

不仅如此，一些地学相关的子刊也有很高的学习价值：

* Nature Climate Change

![image-20230603200110003](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603200110003.png)

* Nature Water

![image-20230603200151042](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603200151042.png)

* Nature Geoscience

![image-20230603200301313](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603200301313.png)

对于我们来说，Nature期刊的封面具有以下学习价值：

1. 提高视觉设计及排版能力：Nature期刊的封面设计非常精美，可以从中学习到如何设计出有吸引力的视觉效果，以及如何进行有效的排版和配色。
2. 加深对研究成果的理解：Nature期刊的封面与其中的研究成果相关，通过仔细观察封面可以更好地理解研究成果及其意义。
3. 学习如何展示研究成果：Nature期刊的封面突出研究成果的亮点和创新点，可以帮助我们更好地展示和传播自己的研究成果，提高研究成果的影响力和受众的关注度。

## 下载方式

小编已经整理好了Nature正刊及相关子刊的封面，可以直接下载，见文末：

![image-20230603200432101](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603200432101.png)

## 代码介绍

以下载正刊的封面为例，用Python BeautifulSoup模块进行解析：

首先设置路径，会在当前

```
import requests
from bs4 import BeautifulSoup
import os

path = 'D:/OneDrive/myprofile/nature 封面/nature 正刊'
if not os.path.exists(path):
    os.makedirs(path)
    print("新建文件夹 nature正刊")
```

设置网页参数：

```
# 设置起始与结束volume
start_volume = 618
end_volume = 500
# nature_url = 'https://www.nature.com/ng/volumes/' # nature genetics
nature_url='https://www.nature.com/nature/volumes/'  # nature 正刊
kv = {
    'User-Agent':
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}
```

其中https://www.nature.com/nature/volumes可以看到各个Volumes号：

![image-20230603202640144](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603202640144.png)

每个Volume中有一些封面：

![image-20230603202719971](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603202719971.png)

只需要修改代码的``start_volume = 618``和``end_volume = 500``设置想要下载的volumes号即可。

接下来进行下载：

```
while start_volume >= end_volume:
    try:
        volume_url = nature_url + str(start_volume)
        volume_response = requests.get(url=volume_url, headers=kv, timeout=120)
    except Exception:
        print(str(start_volume) + "请求异常")
        with open(path + "\\异常.txt", 'at') as txt:
            txt.write(str(start_volume) + "请求异常\n")
        continue
    volume_response.encoding = 'utf-8'
    volume_soup = BeautifulSoup(volume_response.text, 'html.parser')
    ul_tag = volume_soup.find_all(
        'ul',
        class_=
        'ma0 clean-list grid-auto-fill grid-auto-fill-w220 very-small-column medium-row-gap'
    )
    img_list = ul_tag[0].find_all("img")
    issue_number = 0
    for img_tag in img_list:
        issue_number += 1
        filename = path + '\\' + str(start_volume) + '_' + str(
            issue_number) + '.png'
        if os.path.exists(filename):
            print(filename + "已经存在")
            continue
        print("Loading...........................")
        img_url = 'https:' + img_tag.get("src").replace("w200", "w1000")
        try:
            img_response = requests.get(img_url, timeout=240, headers=kv)
        except Exception:
            print(start_volume, issue_number, '???????????异常????????')
            with open(path + "\\异常.txt", 'at') as txt:
                txt.write(
                    str(start_volume) + '_' + str(issue_number) + "请求异常\n")
            continue
        with open(filename, 'wb') as imgfile:
            imgfile.write(img_response.content)
        print("成功下载图片：" + str(start_volume) + '_' + str(issue_number))
    start_volume -= 1
```

我们使用``find_all( name , attrs , recursive , text , **kwargs )``来搜索当前tag的所有tag子节点，检索img，然后循环下载

## 下载子刊封面

由于每本期刊的网页架构不太一样，下载子刊时，我们使用如下代码：

```
import requests
from bs4 import BeautifulSoup
import os


nature_journal = {
    # 要下载的期刊放这里
    'nature plants': 'nplants',
    'nature biotechnology': 'nbt',
    'nature food': 'natfood',
    'nature ecology & evolution': "natecolevol",
    "Nature Climate Change": "nclimate",
    "Nature Water": "natwater",
    "Nature Geoscience": "ngeo"
}
folder_Name = "nature 封面"
kv = {
    'User-Agent':
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36'
}


def makefile(path):
    folder = os.path.exists(path)
    if not folder:
        os.makedirs(path)
        print("Make file -- " + path + " -- successfully!")
    else:
        raise AssertionError



################################################################
def getCover(url, journal, year, filepath, startyear=2022, endyear=2017):
    # 注意endyear是比startyear小的,因为是从endyear开始由后往前来下载的
    if not (endyear <= year <= startyear):
        return
    try:
        issue_response = requests.get("https://www.nature.com" + url,
                                      timeout=120,
                                      headers=kv)
    except Exception:
        print(journal + "  " + str(year) + "  Error")
        return
    issue_response.encoding = 'utf-8'
    if 'Page not found' in issue_response.text:
        print(journal + "  Page not found")
        return
    issue_soup = BeautifulSoup(issue_response.text, 'html.parser')
    cover_image = issue_soup.find_all("img", class_='image-constraint pt10')
    for image in cover_image:
        image_url = image.get("src")
        print("Start loading img.............................")
        image_url = image_url.replace("w200", "w1000")
        if (image_url[-2] == '/'):
            month = "0" + image_url[-1]
        else:
            month = image_url[-2:]
        image_name = nature_journal[journal] + "_" + str(
            year) + "_" + month + ".png"
        if os.path.exists(filepath + journal + "\\" + image_name):
            print(image_url + " 已经存在")
            continue
        print(image_url)
        try:
            image_response = requests.get("http:" + image_url,
                                          timeout=240,
                                          headers=kv)
        except Exception:
            print("获取图片异常:" + image_name)
            continue
        with open(filepath + journal + "\\" + image_name,
                  'wb') as downloaded_img:
            downloaded_img.write(image_response.content)


def main():
    try:
        path = os.getcwd() + '\\'
        makefile(path + folder_Name)
    except Exception:
        print("文件夹 --nature 封面-- 已经存在")
    path = path + folder_Name + "\\"
    for journal in nature_journal:
        try:
            makefile(path + journal)
        except AssertionError:
            print("File -- " + path + " -- has already exist!")
        try:
            volume_response = requests.get("https://www.nature.com/" +
                                           nature_journal[journal] +
                                           "/volumes",
                                           timeout=120,
                                           headers=kv)
        except Exception:
            print(journal + " 异常")
            continue
        volume_response.encoding = 'utf-8'
        volume_soup = BeautifulSoup(volume_response.text, 'html.parser')
        volume_list = volume_soup.find_all(
            'ul',
            class_=
            'clean-list ma0 clean-list grid-auto-fill medium-row-gap background-white'
        )
        
        number_of_volume = 0
        for volume_child in volume_list[0].children:
            if volume_child == '\n':
                continue
            issue_url = volume_child.find_all("a")[0].get("href")
            print(issue_url)
            print(2022 - number_of_volume)
            getCover(issue_url,
                     journal,
                     year=(2022 - number_of_volume),
                     filepath=path,
                     startyear=2022, 
                     endyear=2017)
            number_of_volume += 1


if __name__ == "__main__":
    main()
    print("Finish Everything!")

```

首先先找到想下载的子刊封面合集：

https://www.nature.com/siteindex#journals-N

在上述的网站中找到子刊的名称和架构，一一对应写到代码开始的字典中：

![image-20230603203312468](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603203312468.png)

```
nature_journal = {
    # 要下载的期刊放这里
    'nature plants': 'nplants',
    'nature biotechnology': 'nbt',
    'nature food': 'natfood',
    'nature ecology & evolution': "natecolevol",
    "Nature Climate Change": "nclimate",
    "Nature Water": "natwater",
    "Nature Geoscience": "ngeo"
}
```

如Nature Climate Change是``nclimate``

接下来代码会创建各个子文件夹：

![image-20230603203436828](https://imagecollection.oss-cn-beijing.aliyuncs.com/legion/image-20230603203436828.png)

最后只需要修改想要的年份就能下载对应年份的封面了。

并且，随着日期的更新，重复运行代码就可以自动下载新的封面，跳过已经下载过的封面。