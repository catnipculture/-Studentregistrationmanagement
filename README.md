# 项目介绍

基于ssm的学籍管理系统，学生选课系统，学生管理系统
本系统有管理员、老师、学生三种权限
管理员有：专业管理、班级管理、学生管理、老师管理、课程管理、开课管理、用户管理、审批管理
老师有：成绩管理、学生查询、申请审批
学生有：选课管理、成绩查询、申请管理
本项目用的是ssm框架 

# 环境要求

1.运行环境：最好是java jdk1.8,我们在这个平台上运行的。其他版本理论上也可以。 

2.IDE环境：IDEA,Eclipse,Myeclipse都可以。推荐IDEA; 

3.tomcat环境：Tomcat7.x,8.X,9.x版本均可 

4.硬件环境：windows7/8/10 4G内存以上；或者Mac OS; 

5.是否Maven项目：是；查看源码目录中是否包含pom.xml;若包含，则为maven项目，否则为非maven.项目 

6.数据库：MySql5.7/8.0等版本均可；

# 技术栈

后台框架：Spring MVC、MyBatis

数据库：MySQL

环境：JDK8、TOMCAT、IDEA

# 使用说明

1.使用Navicati或者其它工具，在mysql中创建对应sq文件名称的数据库，并导入项目的sql文件； 

2.使用IDEA/Eclipse/MyEclipse导入项目，修改配置，运行项目； 

3.将项目中config-propertiesi配置文件中的数据库配置改为自己的配置，然后运行；

# 运行指导

idea导入源码空间站顶目教程说明(Vindows版)-ssm篇：

http://mtw.so/5MHvZq 

源码地址：[http://codegym.top](http://codegym.top/)。 


# 运行截图

## 界面

![QQ截图20240117162525](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/a3464d05-f5e6-49d3-99af-c13959069d83)
![QQ截图20240117162426](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/067aa1b6-d592-4784-989c-8487013355aa)
![QQ截图20240117162635](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/1a926bc7-661c-47f2-a211-ddaf599d4bfb)
![QQ截图20240117162559](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/0f906a55-633e-4111-8608-dedcbd5faf07)
![QQ截图20240117162550](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/cfd411a4-ff54-4c9a-90e9-472e46a42629)
![QQ截图20240117162543](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/886d1906-5f45-486b-83af-7631387a20a1)
![QQ截图20240117162535](https://github.com/catnipculture/-Studentregistrationmanagement/assets/126983311/28870146-4400-4c31-8f72-2d8af182454b)


## 代码

CaptchaController

```java
package com.niudada.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.imageio.ImageIO;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Random;

@Controller
@RequestMapping("/captcha")
public class CaptchaController {

    private char[] codeSequence = {'A', '1', 'B', 'C', '2', 'D', '3', 'E', '4', 'F', '5', 'G', '6', 'H', '7', 'I', '8', 'J',
            'K', '9', 'L', '1', 'M', '2', 'N', 'P', '3', 'Q', '4', 'R', 'S', 'T', 'U', 'V', 'W',
            'X', 'Y', 'Z'};

    @RequestMapping("/code")
    public void getCode(HttpServletResponse response, HttpSession session) throws IOException {
        int width = 80;
        int height = 37;
        Random random = new Random();
        //设置response头信息
        //禁止缓存
        response.setHeader("Pragma", "No-cache");
        response.setHeader("Cache-Control", "no-cache");
        response.setDateHeader("Expires", 0);

        //生成缓冲区image类
        BufferedImage image = new BufferedImage(width, height, 1);
        //产生image类的Graphics用于绘制操作
        Graphics g = image.getGraphics();
        //Graphics类的样式
        g.setColor(this.getColor(200, 250));
        g.setFont(new Font("Times New Roman", 0, 28));
        g.fillRect(0, 0, width, height);
        //绘制干扰线
        for (int i = 0; i < 40; i++) {
            g.setColor(this.getColor(130, 200));
            int x = random.nextInt(width);
            int y = random.nextInt(height);
            int x1 = random.nextInt(12);
            int y1 = random.nextInt(12);
            g.drawLine(x, y, x + x1, y + y1);
        }

        //绘制字符
        String strCode = "";
        for (int i = 0; i < 4; i++) {
            String rand = String.valueOf(codeSequence[random.nextInt(codeSequence.length)]);
            strCode = strCode + rand;
            g.setColor(new Color(20 + random.nextInt(110), 20 + random.nextInt(110), 20 + random.nextInt(110)));
            g.drawString(rand, 13 * i + 6, 28);
        }
        //将字符保存到session中用于前端的验证
        session.setAttribute("captcha", strCode.toLowerCase());
        g.dispose();

        ImageIO.write(image, "JPEG", response.getOutputStream());
        response.getOutputStream().flush();
    }

    public Color getColor(int fc, int bc) {
        Random random = new Random();
        if (fc > 255)
            fc = 255;
        if (bc > 255)
            bc = 255;
        int r = fc + random.nextInt(bc - fc);
        int g = fc + random.nextInt(bc - fc);
        int b = fc + random.nextInt(bc - fc);
        return new Color(r, g, b);
    }

}
```



ClazzController

```java
package com.niudada.controller;

import com.niudada.entity.Clazz;
import com.niudada.entity.Subject;
import com.niudada.service.ClazzService;
import com.niudada.service.SubjectService;
import com.niudada.utils.MapControl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Map;

@Controller
@RequestMapping("clazz")
public class ClazzController {

    private static final String LIST = "clazz/list";
    private static final String ADD = "clazz/add";
    private static final String UPDATE = "clazz/update";

    @Autowired
    private ClazzService clazzService;
    @Autowired
    private SubjectService subjectService;

    //跳转添加页面
    @GetMapping("/add")
    public String create(ModelMap modelMap) {
        //查询所有的专业，存储到request域
        List<Subject> subjects = subjectService.query(null);
        modelMap.addAttribute("subjects", subjects);
        return ADD;
    }

    //添加操作
    @PostMapping("/create")
    @ResponseBody
    public Map<String, Object> create(@RequestBody Clazz clazz) {
        int result = clazzService.create(clazz);
        if (result <= 0) {
            return MapControl.getInstance().error().getMap();
        }
        return MapControl.getInstance().success().getMap();
    }

    //根据id删除
    @PostMapping("/delete/{id}")
    @ResponseBody
    public Map<String, Object> delete(@PathVariable("id") Integer id) {
        int result = clazzService.delete(id);
        if (result <= 0) {
            return MapControl.getInstance().error().getMap();
        }
        return MapControl.getInstance().success().getMap();
    }

    //批量删除
    @PostMapping("/delete")
    @ResponseBody
    public Map<String, Object> delete(String ids) {
        int result = clazzService.delete(ids);
        if (result <= 0) {
            return MapControl.getInstance().error().getMap();
        }
        return MapControl.getInstance().success().getMap();
    }

    //修改
    @PostMapping("/update")
    @ResponseBody
    public Map<String, Object> update(@RequestBody Clazz clazz) {
        int result = clazzService.update(clazz);
        if (result <= 0) {
            return MapControl.getInstance().error().getMap();
        }
        return MapControl.getInstance().success().getMap();
    }

    //根据id查询，跳转修改页面
    @GetMapping("/detail/{id}")
    public String detail(@PathVariable("id") Integer id, ModelMap modelMap) {
        //查询所有的专业
        List<Subject> subjects = subjectService.query(null);
        //查询出要修改的班级的信息
        Clazz clazz = clazzService.detail(id);
        //将查询出来的数据存储到request域，实现表单回显
        modelMap.addAttribute("clazz", clazz);
        modelMap.addAttribute("subjects", subjects);
        return UPDATE;
    }

    //查询所有
    @PostMapping("/query")
    @ResponseBody
    public Map<String, Object> query(@RequestBody Clazz clazz) {
        //查询所有的班级
        List<Clazz> list = clazzService.query(clazz);
        //查询所有的专业
        List<Subject> subjects = subjectService.query(null);
        //设置关联
        list.forEach(entity -> {
            subjects.forEach(subject -> {
                //判断班级表中subjectId与专业表的id是否一致
                if (entity.getSubjectId() == subject.getId()) {
                    entity.setSubject(subject);
                }
            });
        });
        //查询班级总数
        Integer count = clazzService.count(clazz);
        return MapControl.getInstance().success().page(list, count).getMap();
    }

    //跳转列表页面
    @GetMapping("/list")
    public String list() {
        return LIST;
    }

}
```
