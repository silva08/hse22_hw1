# hse22_hw1
##Сначала создаем доступ к файлам при помощи команд
>ln -s /usr/share/data-minor-bioinf/assembly/ 
##Создаем папку hw1
>mkdir hw1
## Берем рандомные случайные чтения при SEED = 1208
>seqtk sample -s1208 oil_R1.fastq 5000000 > paired-end1.fastq
>
>seqtk sample -s1208 oil_R2.fastq 5000000 > paired-end2.fastq
>
>seqtk sample -s1208 oilMP_S4_L001_R1_001.fastq 1500000 > mate_pairs1.fastq

seqtk sample -s1208 oilMP_S4_L001_R2_001.fastq 1500000 > mate_pairs2.fastq
## Создаем папку, в которую будут отправлены результаты оценки качества чтений fastqc и передаем полученные файлы с рандомными случайными чтениями на обработку программой fastqc
>mkdir fastqc
>ls pai* mat* | xargs -tI{} fastqc -o fastqc {}   
## Создаем папку, в которую будут отправлены результаты обработки multiqc
>mkdir multiqc
>multiqc -o multiqc fastqc
## Отчет по анализу данных
![image](https://user-images.githubusercontent.com/115162216/194705482-889a9339-3c02-4e1f-a5dd-df9658cd9ad0.png)
![image](https://user-images.githubusercontent.com/115162216/194705505-a1c460d9-3754-4e58-8329-33a950325fd0.png)
![image](https://user-images.githubusercontent.com/115162216/194705518-8be0183f-74fd-4f34-b93f-22d02457b1c2.png)
![image](https://user-images.githubusercontent.com/115162216/194705530-102d51a1-e648-42b2-9066-a7bf54e1bcbf.png)
![image](https://user-images.githubusercontent.com/115162216/194705545-7044a13b-16ac-4d5b-838e-8681d60304c0.png)

## Подрезать чтения по качеству и удаляем адаптеры
>platanus_trim paired_end1.fastq paired_end2.fastq 
>platanus_internal_trim mate_pairs1.fastq mate_pairs2.fastq
## Дальше удаляем исходные файлы в программе MobaXterm
## Перемещаем подрезанные файлы в соответствующую папку и проводим оценку качества чтений с помощью fastqc
>mkdir trimmed_fastqc 
>mv -v *trimmed trimmed   
>ls trimmed/*| xargs -tI{} fastqc -o trimmed_fastqc {}
## Проводим оценку с помощью multiqc результаты помещаем в папку
>mkdir trimmed_multiqc   
>multiqc -o trimmed_multiqc trimmed_fastqc          

## Отчет по анализу обработанных данных 
![image](https://user-images.githubusercontent.com/115162216/194723756-45deea30-1ace-4f12-a67a-5e7b75df6c93.png)
![image](https://user-images.githubusercontent.com/115162216/194723800-fb68358a-2ce7-4de9-9818-8f8f53580476.png)
![image](https://user-images.githubusercontent.com/115162216/194723847-90977564-45ac-43f4-8b90-1e29a646a74c.png)
![image](https://user-images.githubusercontent.com/115162216/194725487-cfd6d573-b428-4ee1-8fe8-777f6a323363.png)

## Собираем контиги прочитанных чтений platanus assemble
>time platanus assemble -o Poil -t 8 -n 20 -f paired_end1.fastq.trimmed paired_end2.fastq.trimmed 2> /home/aasilvestrova/hw1/platanus/Final1.log&
![image](https://user-images.githubusercontent.com/115162216/194952691-9a27bf11-73ca-4e26-b34d-136b0427d139.png)

## Собираем скаффолды из контигов platanus scaffold
>time platanus scaffold -o Poil -c Poil_contig.fa -IP1 paired_end1.fastq.trimmed paired_end2.fastq.trimmed -OP2 mate_pairs1.fastq.int_trimmed mate_pairs2.fastq.int_trimmed 2>Scaffold.log &
![image](https://user-images.githubusercontent.com/115162216/194952724-bf15b259-c0da-4450-ba3a-45ae4af9483f.png)

## Уменьшим количество гэпов
>time platanus gap_close -o Poil -c Poil_contig.fa -IP1 paired_end1.fastq.trimmed paired_end2.fastq.trimmed -OP2 mate_pairs1.fastq.int_trimmed mate_pairs2.fastq.int_trimmed 2>Gaps.log &
![image](https://user-images.githubusercontent.com/115162216/194953154-5f939458-077e-4609-ae06-3e9d083fe98c.png)

## Ссылка на колаб
https://colab.research.google.com/drive/16w3yBFgeEyEzoUqBKcjw5nhE2p885GJ0#scrollTo=rbOJ1sd5jdRg
## Бонусная часть
Выполняем все то же самое, но для меньших прочтений
![image](https://user-images.githubusercontent.com/115162216/194957959-703706c7-c8b4-4145-ba7d-00158472e6ad.png)
![image](https://user-images.githubusercontent.com/115162216/194958036-41eed0c6-5584-4827-a061-18312a523596.png)

## Анализ контигов
![image](https://user-images.githubusercontent.com/115162216/194955378-6be402ff-2e8a-4c39-9ecd-2021a851af87.png)
## Анализ скаффолдов
![image](https://user-images.githubusercontent.com/115162216/194955544-151479f3-8472-4326-9662-bc1c0c55384d.png)
## Анализ с уменьшенным количеством гэпов
![image](https://user-images.githubusercontent.com/115162216/194956737-6ee259c6-5d85-4da9-8821-6e290694ba1c.png)
## Выводы
количество скаффолдов и контигов увеличилось при уменьшении количества чтений,
