LaTeX
===

## tabular で横並びの図がcaptionの文量の違いによってずれる

`\minipate`に[t]を追加して、ページ内で画像を上に揃える

```latex
\begin{figure}[htbp]
	\begin{tabular}{cccc}
		\hspace{-0.4cm}
		\begin{minipage}[t]{0.25\hsize}
			\centering
			\includegraphics[keepaspectratio, height=2.8cm, angle=0]{fig/ras_flow1.png}
			\subcaption{路肩の歩行者を検出し,路肩の歩行者の意図を認識する}
			\label{fig:ras_flow1}
		\end{minipage}
	\end{tabular}
\end{figure}
```

## 画像を端に寄せたい。文章を回り込ませたい
wrapfigureを使う
`\begin{wrapfigure}{寄せる方向}[文章からどれだけ端にはみ出すか]{幅}`
```latex
\usepackage{wrapfig}

\begin{wrapfigure}{r}[10pt]{0.3\textwidth}
	\vspace{-40pt}
	\centering
	\includegraphics[keepaspectratio, scale=0.2, angle=0]{fig/robot_description_interactive_detect.png}
	\caption{実験で用いる小型移動体.被験者は小型移動体の後ろを追従しながら,認識もしくは制御に介入する.}
	\label{fig:robot}
\end{wrapfigure}
```

## サブキャプションを付けたい
```latex
\usepackage{subcaption}

\subcaption{路肩の歩行者を検出し,路肩の歩行者の意図を認識する}
\label{fig:ras_flow1}
```

### PNGを貼りたい
PNG images do not contain Bounding Box Information, so they are not be able to shown correctly in some tex documents.
The one method is generate Bounding Box Information file(.bb).

    extractbb picture.png

Add edit package include line at Tex configuration part

    \usepackage[]{graphicx} -> \usepackage[dvipdfm]{graphicx}


Then you can use .png files normally.
