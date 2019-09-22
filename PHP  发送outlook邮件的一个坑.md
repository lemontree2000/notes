# 使用PHP CI框架发送outlook邮件的一个坑

## 前言

在做side project的时候需要使用outlook发送邮件，将配置文件配置好之后,发送时一直报以下错误.

```
550 5.7.1 Client does not have permissions to send as this sender
quit: 221 2.0.0 Service closing transmission channel
发生错误，SMTP 错误信息为： 550 5.7.1 Client does not have permissions to send as this sender
无法使用 PHP SMTP。您的服务器设置禁止使用此方法发送 E-mail。
```
字面上意思是说没有发送权限，但是我提供的账号是有发送权限的。

## 分析

源码

```php
        $email_config = [
            'protocol' => 'smtp',
            'smtp_host' => $configs['smtp_host'],
            'smtp_user' => bb@qq.com,
            'smtp_pass' => $configs['smtp_pass'],
            'smtp_port' => $configs['smtp_port'],
            'charset' => 'UTF-8',
            'smtp_crypto' => $configs['smtp_crypto'],
            'mailtype' => 'html',
            'crlf' => "\r\n",
        ];
        
        $this->load->library('email');
        $this->email->initialize($email_config);
        $this->email->from('xx@163.com', 'boob');
        $this->email->to('xx@qq.com');
        $this->email->subject('TItle');
        $this->email->message('sbject');
        $this->email->send();
```
            'newline' => "\r\n"
经过google之后找到答案大部分的都是说没有设置换行符的问题。

> 经过大量研究，我们发现问题与换行符以及SMTP主机解释换行符的方式有关。默认的换行符是\n，而SMTP服务器则期望\r\n。它从来没有发现过，因此认为它从未到尽头，最终导致timeout。

然后我就将配置做了一下修改

```php
        $email_config = [
            'protocol' => 'smtp',
            'smtp_host' => $configs['smtp_host'],
            'smtp_user' => bb@qq.com,
            'smtp_pass' => $configs['smtp_pass'],
            'smtp_port' => $configs['smtp_port'],
            'charset' => 'UTF-8',
            'smtp_crypto' => $configs['smtp_crypto'],
            'mailtype' => 'html',
            'crlf' => "\r\n",
        ];

```

配置修改后还没有解决问题，于是我接着google上面找相似问题的答案。

无意间在百度知道看到一个答案

> 其实Error里说的很明白了
Error Message: 550 5.7.1 Client does not have permissions to send as this sender
用户无Send as权限。
请检查一下你的Outlook上的From是否是自己的账号

## 解决

我检查了发送人，确实和smtp_user 不一样，接着我就把form 改成 smtp_user 测试了一下，结果发送成功了。

```php
    // from 也改成了 bb@qq.com
    // 'smtp_user' => bb@qq.com,
    $this->load->library('email');
    $this->email->initialize($email_config);
    $this->email->from('bb@qq.com,', 'boob');
    $this->email->to('xx@qq.com');
    $this->email->subject('TItle');
    $this->email->message('sbject');
    $this->email->send();
```

这让我产生了怀疑， 因为我之前做邮件发送的时候不需要填和smtp_user一直的from 邮箱，抱着这个问题我去微软官网了解了一下outlook ，发现outlook有一个机制： 默认禁用匿名发送既smtp_user和form必须一致。

自此才圆满的解决了该问题， 特此写篇note来记录

[微软关于匿名发送的说明](https://docs.microsoft.com/zh-cn/previous-versions/exchange-server/exchange-server-2000/aa997163(v=exchg.65))
