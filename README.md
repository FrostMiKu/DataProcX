# DataProcX

Text label processing framework.


## Example

label.txt file:
```
SSB01120001 /data3/A07/spoof/SSB01120001.wav True
SSB01120002 /data3/A07/spoof/SSB01120002.wav True
SSB01120003 /data3/A07/spoof/SSB01120003.wav False
SSB01120004 /data3/A07/spoof/SSB01120004.wav True
SSB01120006 /data3/A07/spoof/SSB01120006.wav True
SSB01120007 /data3/A07/spoof/SSB01120007.wav False
SSB01120008 /data3/A07/spoof/SSB01120008.wav True
SSB01120009 /data3/A07/spoof/SSB01120009.wav False
SSB01120010 /data3/A07/spoof/SSB01120010.wav False
SSB01120011 /data3/A07/spoof/SSB01120011.wav True
```

main.py
```python
import DataProcX as dp

p = dp.Parser()

# function hook, preprocess every line txt, str -> str
p.preprocess = lambda x: x.strip() 

d = p.loadFile('label/label.txt', 0, ' ')
tmp = d.head(4)
print(tmp)
# [['SSB01120001' '/data3/A07/spoof/SSB01120001.wav' 'True'] 
#  ['SSB01120002' '/data3/A07/spoof/SSB01120002.wav' 'True'] 
#  ['SSB01120003' '/data3/A07/spoof/SSB01120003.wav' 'False']
#  ['SSB01120004' '/data3/A07/spoof/SSB01120004.wav' 'True']]

# function hook, postprocess be parsed every line list, list -> list
p.postprocess = lambda x: x[1:]
d = p.parse(tmp.head(3).col(1),0,'/')
print(d)
# [['data3' 'A07' 'spoof' 'SSB01120001.wav'] 
#  ['data3' 'A07' 'spoof' 'SSB01120002.wav'] 
#  ['data3' 'A07' 'spoof' 'SSB01120003.wav']]

# same
p.postprocess = lambda x: list(map(lambda y: y.strip('.wav')+'_proc', x[1:]))
d = p.parse(tmp.head(3).col(1),0,'/')
print(d)
# [['data3_proc' 'A07_proc' 'spoof_proc' 'SSB01120001_proc']
#  ['data3_proc' 'A07_proc' 'spoof_proc' 'SSB01120002_proc']
#  ['data3_proc' 'A07_proc' 'spoof_proc' 'SSB01120003_proc']]

# save to file
d.toCsv('label/label_proc.csv')

```

label_proc.csv (output file)
```
data3_proc,A07_proc,spoof_proc,SSB01120001_proc
data3_proc,A07_proc,spoof_proc,SSB01120002_proc
data3_proc,A07_proc,spoof_proc,SSB01120003_proc
```