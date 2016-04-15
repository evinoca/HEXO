---
title: 简单问题简单处理: Perl来汇总零散的Txt文件
date: 2016-04-14 19:03:48
categories: Perl
tags: [Perl,自娱自乐]
---

背景
===
有一阵公司特别忙，管理层对于我们这些基层人员的工作进度非常的上心。
但是公司实在太小，连OA系统都没有配置，也没有项目管理协同办公的工具。

全程靠吼和qq，真是low爆了...

这说了，没个人都要写日报，每天汇总到一起来发项目组日报。
汇总方案如下：

1. 发邮件，专人汇总
2. 在svn上面写一个记事本，谁写谁checkout，写完再update
3. ...没了

我心说这都什么主意啊...
一个组里有20多口子人，这要是用邮件汇总，负责贴日报的兄弟每天光收拾这个邮件怎么也得干十来分钟吧，要是让我每天都干这档子事儿肯定得把头发挠下来一块儿
用SVN这么多人用一个文件，那还不如写邮件呢...

要让我写个OA我可是没时间，也没那技术积累。
还是写个脚本吧，这个简单，分分钟搞定

<!--more-->
主要流程
===
需要做的工作很简单

1. 在当前目录新建一个文本文件
2. 在目标SVN路径下查找所有符合条件的txt日志
3. 把所有发现的日志文件中符合当前日期的日志内容取出来，都保存在新建的文本文件中
4. 保存文件

按照这个思路，一个模块一个模块来就行了

收集文件
===
想汇总日志，第一件事儿就是把大家的日志先收上来。
都不用搞啥花活儿了，直接用windows记事本就得了，还有F5快捷时间戳，正则表达也好拼。
用Python感觉还是有点儿重了，所以决定使用perl来写一段

因为原来写过这种搬文件的东西，这里可以直接拿过来用

```Perl
foreach my $file (glob "*.txt") 取当前目录下的所有txt为后缀文件
	{
		#print $file; #$file为文件名
  }
```


打开文本文件
===

```perl
#!/usr/perl

open(REPORT,">".$reportFileName) or die $!;#$reportFileName为目标文件名
syswrite(REPORT,"$content");#写文件
close(REPORT);

```
生成时间戳
===
这个也是我工具箱里面必备部分，直接用。
方法返回一个hash表

```perl
#!/usr/perl

sub getTime
{
   #time()函数返回从1970年1月1日起累计秒数
    my $time = shift || time();

    my ($sec,$min,$hour,$mday,$mon,$year,$wday,$yday,$isdst) = localtime($time);

    $mon ++;
    $sec  = ($sec<10)?"0$sec":$sec;#秒数[0,59]
    $min  = ($min<10)?"0$min":$min;#分数[0,59]
    $hour = ($hour<10)?"0$hour":$hour;#小时数[0,23]
    $mday = ($mday<10)?"0$mday":$mday;#这个月的第几天[1,31]
    my $monF  = ($mon<10)?"0$mon":$mon;#月数[0,11],要将$mon加1之后，才能符合实际情况。
    $year+=1900;#从1900年算起的年数

    #$wday从星期六算起，代表是在这周中的第几天[0-6]
    #$yday从一月一日算起，代表是在这年中的第几天[0,364]
  # $isdst只是一个flag
    my $weekday = ('Sun','Mon','Tue','Wed','Thu','Fri','Sat')[$wday];
    return { 'second' => $sec,
             'minute' => $min,
             'hour'   => $hour,
             'day'    => $mday,
             'month'  => $mon,
			 'month_F'  => $monF,
             'year'   => $year,
             'weekNo' => $wday,
             'wday'   => $weekday,
             'yday'   => $yday,
             'date'   => "$year/$mon/$mday",
			 'dateF'   => "$year$monF$mday"
          };
}
```
代码拼装
===
把之前三个模块直接拼在一起，查找文件并打开文件，取文本内容，再做正则表达式匹配查找日志文件作者

```Perl
#!/usr/perl


use strict;
use File::stat;
use File::Copy;
use Net::Telnet;
use File::Basename;

my $work_path =  dirname(__FILE__);

print $work_path."\n";
chdir $work_path;

sub getTime
{
   #time()函数返回从1970年1月1日起累计秒数
    my $time = shift || time();

    my ($sec,$min,$hour,$mday,$mon,$year,$wday,$yday,$isdst) = localtime($time);

    $mon ++;
    $sec  = ($sec<10)?"0$sec":$sec;#秒数[0,59]
    $min  = ($min<10)?"0$min":$min;#分数[0,59]
    $hour = ($hour<10)?"0$hour":$hour;#小时数[0,23]
    $mday = ($mday<10)?"0$mday":$mday;#这个月的第几天[1,31]
    my $monF  = ($mon<10)?"0$mon":$mon;#月数[0,11],要将$mon加1之后，才能符合实际情况。
    $year+=1900;#从1900年算起的年数

    #$wday从星期六算起，代表是在这周中的第几天[0-6]
    #$yday从一月一日算起，代表是在这年中的第几天[0,364]
  # $isdst只是一个flag
    my $weekday = ('Sun','Mon','Tue','Wed','Thu','Fri','Sat')[$wday];
    return { 'second' => $sec,
             'minute' => $min,
             'hour'   => $hour,
             'day'    => $mday,
             'month'  => $mon,
			 'month_F'  => $monF,
             'year'   => $year,
             'weekNo' => $wday,
             'wday'   => $weekday,
             'yday'   => $yday,
             'date'   => "$year/$mon/$mday",
			 'dateF'   => "$year$monF$mday"
          };
}

#main

my $date = &getTime();#获取当前系统时间的Hash
my $timestaemp = $date->{dateF};#获取yyyymmdd这样的日期  
my $timeRegexpString= $date->{date};
my $reportFileName= "QA_DailyReport_$timestaemp.txt";
print $reportFileName."\n";
print $timeRegexpString."\n";

open(REPORT,">".$reportFileName) or die $!;

foreach my $file (glob "*.txt")
	{
		#print $file;
		if ($file=~m/QA/)
		{			
		}
		elsif ($file=~m/(.*).txt/)
		{
			my $rwFlag= 0;
			print "Report: ".$1."\n";
			syswrite(REPORT,$1." @ ");		
			open FILE, $file or die $!;
			chomp (my @lines = (<FILE>));
			close(FILE);
			my $i = 1;
			foreach my $line (@lines)
			{
			   #print $line."\n";
			   if ($line=~m/$timeRegexpString/)
			   {
					$rwFlag= 1;
			   }
			   elsif($line=~m/\d{4}\/\d?\/\d?/)
			   {
					$rwFlag= 0;
			   }
			   if ($rwFlag== 1)
			   {
					syswrite(REPORT,$line."\n");					
			   }

			}
		}
	}

	close(REPORT);

```

优化
===
后面调试了一下，觉得还是差了一点儿东西。
做了一部分调整
1. 添加了格式化输出，直接在命令行打印日志提交状态
2. 对子工作组的命名规则做了正则匹配

```perl
#!/usr/perl
use utf8;
use strict;
use File::stat;
use File::Copy;
use File::Basename;
use Data::Dumper;
use Encode;

#获取系统路径并切换路径
my $work_path =  dirname(__FILE__);
chdir $work_path or die $!;

opendir(DIR, $work_path."//DailyReport") or die $!;
my @filename = readdir(DIR);
#print Dumper(@filename);
@filename= sort {$a cmp $b} @filename;
#print Dumper(@filename);
my $archiveFolder = "Archived";

#my %teamOrg = ('AT',"Automation",'ST',"System_Test",'MT',"Mobile_Test");
my %teamOrg = ('AT',"Automation",'ST',"System_Test");

sub getTime
{
   #time()函数返回从1970年1月1日起累计秒数
    my $time = shift || time();
    my ($sec,$min,$hour,$mday,$mon,$year,$wday,$yday,$isdst,$monF) = localtime($time);
    $mon ++;
    $sec  = ($sec<10)?"0$sec":$sec;#秒数[0,59]
    $min  = ($min<10)?"0$min":$min;#分数[0,59]
    $hour = ($hour<10)?"0$hour":$hour;#小时数[0,23]
    $mday = ($mday<10)?"0$mday":$mday;#这个月的第几天[1,31]
    $monF  = ($mon<10)?"0$mon":$mon;#月数[0,11],要将$mon加1之后，才能符合实际情况。
    $year+=1900;
    my $weekday = ('Sun','Mon','Tue','Wed','Thu','Fri','Sat')[$wday];
    return { 'second' => $sec,
             'minute' => $min,
             'hour'   => $hour,
             'day'    => $mday,
             'month'  => $mon,
			 'month_F'  => $monF,
             'year'   => $year,
             'weekNo' => $wday,
             'wday'   => $weekday,
             'yday'   => $yday,
             'date'   => "$year/$mon/$mday",
			 'dateF'   => "$year$monF$mday"
          };
}

#main
my $date = &getTime();#获取当前系统时间的Hash
my $timestamp = $date->{dateF};#获取yyyymmdd这样的日期
my $timeRegexpString= $date->{date};
my %detail;
my %counter;

foreach my $t (keys %teamOrg)
{
	$counter{$t}{'updated'}=0;
	$counter{$t}{'profile'}=0;
	$counter{$t}{'ReportFile'}="QA_".$teamOrg{$t}."_DailyReport_$timestamp.txt";
	open($counter{$t}{'fileHandler'},">".$counter{$t}{'ReportFile'}) or die $!;
}
#print Dumper(%counter);
chdir $work_path."//DailyReport" or die "Can't find path DailyReport".$!;

#foreach my $file (glob "*.txt")
foreach my $file (@filename)
	{
		#print $file;
		if ($file=~m/(.*)_(.*).txt/)
		{		
			my $team = $1;
			my $teamMember = $2;
			next if not exists($teamOrg{$team});
			my $teamFullName = $teamOrg{$1};
			my $rwFlag= 0;
			my $submitFlag = 0;
			$counter{$team}{'profile'}++;
			syswrite($counter{$team}{'fileHandler'},$teamMember." @ ") or die "Can't write report file".$!;
			open FILE, $file or die $!;
			chomp (my @lines = (<FILE>));
			close(FILE);

			foreach my $line (@lines)
			{
			   if ($line=~m/$timeRegexpString/)
			   {
					$rwFlag= 1;
			   }
			   elsif($line=~m/\d{4}\/\d?\/\d?/)
			   {
					$rwFlag= 0;
			   }
			   if ($rwFlag== 1)
			   {
					syswrite($counter{$team}{'fileHandler'},$line."\r\n") or die "Can't write report file".$!;
					$submitFlag=1;
			   }				
			}
			if ($submitFlag == 0)
			{
				$detail{"NotUpdate"}{$teamMember}{"status"}="Not Submit";
				$detail{"NotUpdate"}{$teamMember}{"team"}=$teamOrg{$team};
				syswrite($counter{$team}{'fileHandler'},"\r\n") or die "Can't write report file".$!;
			}
			else
			{
				$counter{$team}{'updated'}++;
				$detail{"Updated"}{$team}{$teamMember}{"status"}="Updated";
				$detail{"Updated"}{$team}{$teamMember}{"team"}=$teamOrg{$team};
			}
			syswrite($counter{$team}{'fileHandler'},"\r\n") or die "Can't write report file".$!;
		}
		else
		{
		next;
			}
	}
#print Dumper(%detail);
my ($dname, $dteam, $dstatus);
my $updatedHash = $detail{"Updated"};
my $notUpdateHash = $detail{"NotUpdate"};
my $updated_size=keys%$updatedHash;
my $notupdated_size = keys%$notUpdateHash;
#print Dumper($updatedHash);

foreach my $teamHash (sort {$b cmp $a} keys %$updatedHash){
	my $updateTeamHash =  $detail{"Updated"}{$teamHash};
	#print Dumper($updateTeamHash);
	foreach my $name (sort {$b cmp $a} keys %$updateTeamHash)
	{
		($dname, $dteam, $dstatus)= ($name,$detail{"Updated"}{$teamHash}{$name}{"team"},$detail{"Updated"}{$teamHash}{$name}{"status"});
		$^ = 'STDOUT_TOP';
		$~ = 'STDOUT';
		write or die "Error",$!;
	}
}
print "------------------------------------------------------------------\n"  if ($updated_size!=0 or $notupdated_size!=0);
foreach my $name (sort {$b cmp $a} keys %$notUpdateHash)
{
	($dname, $dteam, $dstatus)= ($name,$detail{"NotUpdate"}{$name}{"team"},$detail{"NotUpdate"}{$name}{"status"});
	$^ = 'STDOUT_TOP';
	$~ = 'STDOUT';
	write or die "Error",$!;
}

format STDOUT_TOP =
@<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
$timeRegexpString	  
		Daily Report Status
------------------------------------------------------------------		
Name           Team             Report Status
------------------------------------------------------------------
.

format STDOUT =
@<<<<<<<<<<<<<@<<<<<<<<<<<<<<<<<<@<<<<<<<<<<<<<<<<<<
$dname,                        $dteam,                                   $dstatus
.
print "------------------------------------------------------------------\n";
foreach my $t (keys %teamOrg)
{
	close($counter{$t}{'fileHandler'});
	print $t,": ",$counter{$t}{"updated"},"/",$counter{$t}{"profile"},"\n";
}
print "------------------------------------------------------------------\n";


chdir $work_path or die $!;
mkdir $archiveFolder unless -e $archiveFolder;

opendir(DIR, $work_path) or die $!;
my @filename = readdir(DIR);
foreach my $file (@filename)
{
	if ($file=~m/^QA.*.txt/){
	#print $file."\n";
	my $source = $work_path ."\\".$file;
	my $dest = $work_path ."\\".$archiveFolder."\\".$file;
	move ($work_path ."/".$file,$dest)  unless $file =~m/$timestamp/;
	}
}
system("pause");

```
总结
===
这种小活儿，果然还是用perl来做比较好。
Perl被归类为魔幻语言就是因为什么事儿都会有更好的解决方法。
上面这个脚本以最笨的方式解决了一个更笨的需求。

P.S. 这个小工具前后一共使用了1一个月左右，多少节省了一部分劳动力吧。
但要是专门做一个Web App来搞这个小事儿，投入产出结果可是太不值了...
