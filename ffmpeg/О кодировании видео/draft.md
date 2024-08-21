# Полезные ссылки

Здесь можно найти почти все опции. Правда, не всегда можно понять как их применять, т.к. примеров или нет, или они не понятные и приходится искать по интернетам. Но как ориентир, нормально:

* https://ffmpeg.org/ffmpeg-all.html
* http://ffmpeg.org/ffmpeg-codecs.html
* http://www.chaneru.com/Roku/HLS/X264_Settings.htm#ipratio

# Все опции кодека

Все опции, которые можно видеть в mediainfo:

```
cabac=1 
ref=9 
deblock=1:-2:-2 
analyse=0x3:0x113 
me=umh 
subme=11 
psy=1 
fade_compensate=0.00 
psy_rd=1.00:0.00 
mixed_ref=1 
me_range=48 
chroma_me=1 
trellis=2 
8x8dct=1 
cqm=0 
deadzone=21,11 
fast_pskip=0 
chroma_qp_offset=-2 
threads=6 
lookahead_threads=1 
sliced_threads=0 
nr=0 
decimate=0 
interlaced=0 
bluray_compat=0 
constrained_intra=0 
bframes=16 
b_pyramid=2 
b_adapt=2 
b_bias=0 
direct=3 
weightb=1 
open_gop=0 
weightp=2 
keyint=250 
keyint_min=23 
scenecut=40 
intra_refresh=0 
rc_lookahead=60 
rc=2pass 
mbtree=1 
bitrate=12412 
ratetol=1.0 
qcomp=0.60 
qpmin=0 
qpmax=69 
qpstep=4 
cplxblur=20.0 
qblur=0.5 
vbv_maxrate=50000 
vbv_bufsize=62500 
nal_hrd=none 
filler=0 
ip_ratio=1.25
```

# Ключевые опции

Есть два рипа, которые мне очень понравились своим качеством - "В джазе только девушки" и "Возвращение живых мертвецов 2". Понравились они мне главным образом сохранением грейна (grain) - этого "шума", "зерна", который дает ощущение "не пластмассовости" картинки. После обычного рипа с дефолтными настройками это зерно терялось. Поэтому я взял mediainfo из этих двух рипов и сравнил их с инфо из своего рипа, который получается с дефолтными настройками ffmpeg.

Отличия были в следующих настройках: рядом я размещаю параметры ffmpeg, с помощью которых можно эти настройки задать:

```
ref=9            / -x264-params ref=9
deblock=1:-2:-2  / -x264-params deblock=-2
me=umh           / -x264-params me=umh
subme=11         / -x264-params subme=11
psy_rd=1.00:0.00 / -x264-params psy-rd=1.05,1.00
me_range=48      / -x264-params me_range=48
trellis=2        / -trellis 2
fast_pskip=0     / -x264-params fast-pskip=0
threads=6        / не надо 
lookahead_threads=1 / не надо
decimate=0       / -x264opts no-dct-decimate
bframes=16       / -bf 16
b_adapt=2        / -b_strategy 2 (указывается только в первом проходе)
direct=3         / -x264-params direct_pred=auto
keyint=250       / -g 250
rc_lookahead=60  / -x264-params rc-lookahead=60
rc=2pass         / получается, когда кодируешь в два захода
mbtree=1         / -x264-params mbtree=1
bitrate=12412    / -b:v 12412k
ip_ratio=1.25    / -x264-params ipratio=1.25
aq=1:0.70        / -x264-params aq-mode=1:aq-strength=0.70
```

Пример команды с заданием этих настроек: это просто пример, для рипа не подходит, потому что лучше рип делать в двухпроходном режиме:

```
ffmpeg -y -i "movie.m2ts" -c:v libx264 -b:v 5800k -x264-params ref=9:deblock=-2:me=umh:subme=11:psy-rd=1.05,1.00:me_range=48:fast-pskip=0:direct_pred=auto:rc-lookahead=60:mbtree=0:ipratio=1.25:aq-mode=1:aq-strength=0.70 -x264opts no-dct-decimate -trellis 2 -bf 16 -g 250 -c:a copy "out.mp4"
```

## Формат

Некоторые опции задаются через самостоятельные флаги, например, `keyint` задается флагом `-g` и т.д.

Некоторые опции задаются как параметры, около них написано `-x264-params`. Значения задаются в формате `par1=val:par2=val`. 

Особенности:

* Когда вот здесь https://ffmpeg.org/ffmpeg-all.html в настройке есть `-`, например `psy-rd`, это значит что опцию надо писать через `_` например `psy_rd`.
* Если значение опции комплексное и состоит из нескольких значений, которые в доке разделены `:`, это значит что их надо вводить через `,` поскольку двоеточие используется для разделения опций. Например `psy-rd=1.05,1.00`
* Если написать опцию-параметр, которую ffmpeg не сможет распознать, то ошибки не будет, просто в консоли будет строчка о том, что такую-то опцию не удалось понять.

## Сравнение значений

В таблице приведены значения опций из разных рипов:

| Опция        | "Крутые" девушки | Дефолтный рип ffmpeg | Мертвецы    |
| ------------ | ---------------- | -------------------- | ----------- |
| ref          | 9                | 3                    | 9           |
| deblock      | 1 : -2 : -2      | 1 : 0 : 0            | 1 : -3 : -3 |
| me           | umh              | hex                  | umh         |
| subme        | 11               | 7                    | 11          |
| psy_rd       | 1.00 : 0.00      | 1.00 : 0.00          | 1.05 : 0.00 |
| me_range     | 48               | 16                   | 32          |
| trellis      | 2                | 1                    | 2           |
| fast_pskip   | 0                | 1                    | 0           |
| decimate     | 0                | 1                    | 0           |
| bframes      | 16               | 3                    | 8           |
| b_adapt      | 2                | 1                    | 2           |
| direct       | 3                | 1                    | 3           |
| keyint       | 250              | 250                  | 240         |
| rc_lookahead | 60               | 40                   | ???         |
| rc           | 2pass            | abr                  | 2pass       |
| mbtree       | 1                | 1                    | 0           |
| ip_ratio     | 1.25             | 1.40                 | 1.40        |
| aq           | 2 : 1.00         | 1 : 1.00             | 1 : 0.70    |

## Назначение опций

* `ref=9`
* `deblock=1:-2:-2`
* `me=umh`
* `subme=11`
* `psy_rd=1.00:0.00`
* `me_range=48`
* `trellis=2`
* `fast_pskip=0`
* `decimate=0`
* `bframes=16`
* `b_adapt=2`
* `direct=3`
* `keyint=250`
* `rc_lookahead=60`
* `rc=2pass`
* `mbtree=1`
* `ip_ratio=1.25`
* `aq=1:0.70`

# 2pass шаблон

В шаблоне нет дополнительных вещей, вроде начала и конца рипа, преобразования разрешения и т.д. Только все вышеописанные параметры. Значения взяты как в рипе "В джазе только девушки". Только deblock выставлен побольше, как в "Мертвецах":

1 проход:

```
ffmpeg -y -ss 00:33:00 -t 00:00:05 -i "movie.m2ts" -c:v libx264 -b:v 5800k -x264-params ref=9:deblock=-3:me=umh:subme=11:psy-rd=1.00,0:me_range=48:fast-pskip=0:direct_pred=auto:rc-lookahead=60:mbtree=1:ipratio=1.25:aq-mode=2:aq-strength=1.00 -x264opts no-dct-decimate -trellis 2 -bf 16 -g 250 -b_strategy 2 -c:a copy -pass 1 -an -f mp4 NUL

```

2 проход:

```
ffmpeg -y -ss 00:33:00 -t 00:00:05 -i "movie.m2ts" -c:v libx264 -b:v 5800k -x264-params ref=9:deblock=-3:me=umh:subme=11:psy-rd=1.00,0:me_range=48:fast-pskip=0:direct_pred=auto:rc-lookahead=60:mbtree=1:ipratio=1.25:aq-mode=2:aq-strength=1.00 -x264opts no-dct-decimate -trellis 2 -bf 16 -g 250 -c:a copy -pass 2 "out.mp4"
```

