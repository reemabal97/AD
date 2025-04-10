# تصنيف الهجمات باستخدام خوارزمية الغابة العشوائية وتحليل النتائج

## نبذة عن الكود 
### يستخدم هذا الكود خوارزمية "غابة الأشجار العشوائية" (Random Forest) لتصنيف البيانات في مجال الأمان السيبراني، حيث يتم التنبؤ بما إذا كانت السجلات التي يتم فحصها تشير إلى هجوم أم لا بناءً على عدة ميزات تم جمعها. يتم تحميل البيانات، تقسيمها إلى مجموعات تدريب واختبار، ومن ثم تدريب النموذج باستخدام بيانات التدريب. بعد ذلك، يتم تقييم النموذج باستخدام مقاييس متعددة مثل الدقة (accuracy)، دقة التنبؤ (precision)، الاسترجاع (recall)، ومعدل F1. وأخيرًا، يتم عرض نتائج التصنيف بشكل بياني. 


## شرح بعض الاكواد 
* pandas: مكتبة تُستخدم لتحميل البيانات ومعالجتها في شكل DataFrame، وهي بنية بيانات تسهل العمل مع البيانات المهيكلة.
* RandomForestClassifier: خوارزمية تعلم آلي تُستخدم لتصنيف البيانات بناءً على مجموعة من الأشجار العشوائية التي تُحدد قرارات التصنيف بناءً على عدة ميزات.
* train_test_split: تُستخدم لتقسيم البيانات إلى مجموعات تدريب واختبار.
* confusion_matrix: تُستخدم لحساب مصفوفة الالتباس (Confusion Matrix) التي توضح التوزيع بين القيم الحقيقية والمتوقعة.
* precision_score: تقيس مدى دقة التنبؤات بالهجمات.
* recall_score: تقيس مدى قدرة النموذج على اكتشاف الهجمات.
* accuracy_score: تقيس مدى دقة النموذج بشكل عام.
* f1_score: مقياس يجمع بين الدقةو والاسترجاع (recall) (precision) 
* matplotlib.pyplot: مكتبة لرسم البيانات بشكل بياني، تُستخدم لعرض نتائج التصنيف بشكل مرئي.
* OneHotEncoder, LabelEncoder: تُستخدم لترميز الأعمدة الفئوية (مثل النصوص) إلى أرقام بحيث يمكن استخدامها في النماذج.

## شرح خطوات الكود 
### ١. تحميل البيانات 
* نستخدم read_csv() لتحميل البيانات من الملف log_data.csv(ملف ابتكرته ك مثال) إلى DataFrame. هذا يسمح لنا بالعمل مع البيانات بشكل منظم.
```python
  df = pd.read_csv('/Users/reemabalharith/Desktop/log_data.csv')
```
* استبدل رابط الملف  برابطك الخاص
  
### ٢. استكشاف البيانات
* نستخدم df.head() لعرض أول 5 صفوف من البيانات للتأكد من شكل البيانات وفهمها سريعًا.
```python
  print(df.head())
```

### ٣. تحديد الخصائص والهدف:
* X: يحتوي على الأعمدة التي تمثل الخصائص التي ستُستخدم للتنبؤ (مثل: open_ports, login_attempts).
* y: يحتوي على العمود الذي نريد التنبؤ به، وهو is_attack.

```python
X = df[['open_ports', 'login_attempts', 'data_transferred_MB', 'login_time_hour']]
y = df['is_attack']
```

### ٤. تقسيم البيانات إلى تدريب واختبار:
نستخدم train_test_split لتقسيم البيانات إلى:
* X_train و y_train: لاستخدامها في تدريب النموذج.
* X_test و y_test: لاختبار دقة النموذج بعد تدريبه.
```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25, random_state=42)
```

### ٥. تدريب النموذج:
نستخدم RandomForestClassifier لبناء نموذج غابة الأشجار العشوائية، ثم نقوم بتدريبه باستخدام بيانات التدريب.
```python
model = RandomForestClassifier()
model.fit(X_train, y_train)
```

### ٦. التنبؤ باستخدام النموذج:
بعد تدريب النموذج، نستخدمه لتنبؤ الفئات باستخدام بيانات الاختبار.
```python
y_pred = model.predict(X_test)
```
### ٧. حساب مقاييس التقييم:
نحسب مصفوفة الالتباس (Confusion Matrix) التي تُظهر كيف تم تصنيف الهجمات والنشاط الطبيعي:
* TP: الهجمات التي تم تصنيفها بشكل صحيح.
* FP: الأنشطة الطبيعية التي تم تصنيفها بشكل خاطئ كـ هجوم.
* TN: الأنشطة الطبيعية التي تم تصنيفها بشكل صحيح.
* FN: الهجمات التي تم تصنيفها بشكل خاطئ كـ نشاط طبيعي.
```python
tn, fp, fn, tp = confusion_matrix(y_test, y_pred).ravel()
```
### ٨. حساب المقاييس الأخرى:
نستخدم المقاييس التالية لتقييم أداء النموذج:
* accuracy_score: الدقة العامة للنموذج.
* precision_score: دقة النموذج في التنبؤ بالهجمات.
* recall_score: قدرة النموذج على اكتشاف الهجمات.
* f1_score: مقياس التوازن بين الدقة والاسترجاع.
```python
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)
```

### ٩. رسم النتائج بيانيًا :
نستخدم matplotlib لرسم الأعمدة البيانية التي تمثل قيم الـ TP و FP و TN و FN.
```python
labels = ['True Positives', 'False Positives', 'False Negatives', 'True Negatives']
values = [tp, fp, fn, tn]

plt.figure(figsize=(8,6))
bars = plt.bar(labels, values, color=['green', 'orange', 'red', 'blue'])
plt.title('Classification Results')
plt.ylabel('Count')
plt.grid(axis='y', linestyle='--', alpha=0.7)

for bar in bars:
    yval = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, yval + 0.1, int(yval), ha='center', va='bottom')

plt.tight_layout()
plt.show()
```







