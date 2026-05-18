# תיעוד פרויקט: ארכיטקטורה, KPIs ודוגמאות שימוש

פרויקט זה מציג מימוש סימולטני של שני דפוסי ארכיטקטורה מתקדמים (Microservices ו-FaaS), לצד הגדרת מדדי ביצוע (KPIs) ותיעוד מלא של רכיבי המערכת.

---

## 1. מדדי ביצוע מרכזיים (KPIs)

להלן מדדי הביצוע המרכזיים הרלוונטיים לפרויקט ושיטות הבדיקה שלהם:

1. **Performance (ביצועים) - זמן תגובה לניתוח תמונה**
   * **איך בודקים:** שימוש בכלי Load Testing כדוגמת **JMeter** או **Locust** לשליחת בקשות רבות במקביל ומדידת זמני התגובה של המערכת.
2. **Scalability (גמישות וגידול) - עומס משתמשים במקביל**
   * **איך בודקים:** הרצת בדיקות עומס (**Stress Testing**) תוך העלאה הדרגתית של כמות המשתמשים, ומדידת ה-Response Time ו-Error Rate (קצב השגיאות) בכל שלב.
3. **Availability (זמינות) - זמינות המערכת**[cite: 1]
   * **איך בודקים:** הטמעת כלי ניטור ומעקב המודדים **Uptime** רציף ושולחים התראות מיידיות על כל השבתה או נפילה של רכיב במערכת.[cite: 1]
4. **Security (אבטחת מידע) - הצפנת נתונים**[cite: 1]
   * **איך בודקים:** ביצוע מבדקי חדירות (**Penetration Testing**), בדיקת תעודות SSL/TLS וסריקת פגיעויות (**Vulnerabilities**) קבועה במערכת.[cite: 1]
5. **Usability (חווית משתמש) - זמן טעינה**[cite: 1]
   * **איך בודקים:** שימוש בכלים כגון **Google Lighthouse** או **WebPageTest** להרצת בדיקות ביצועי קצה ברשתות ובמכשירים שונים.[cite: 1]

---

## 2. דפוסי ארכיטקטורה במערכת[cite: 1]

המערכת מתוכננת וממומשת תוך שילוב של שני דפוסים במקביל:[cite: 1]

### א. Microservices[cite: 1]
כל שירות אחראי על תחום פונקציונלי אחד בלבד ומתקשר עם השירותים האחרים דרך ממשקים (APIs) מוגדרים היטב:[cite: 1]
UserService ──> IndexService ──> QueryService ──> ResultService


### ב. FaaS (Function as a Service)[cite: 1]
ארכיטקטורה מבוססת אירועים (Event-Driven). כל פונקציה היא Stateless (חסרת מצב) וניתנת להרצה ולייעול במקביל באופן אוטומטי:[cite: 1]
FaaSSimulator ├──> IndexerFunction
└──> SearcherFunction


---

## 3. תיעוד השירותים והפונקציות[cite: 1]

| שירות / פונקציה | אחריות | מתודות עיקריות / נקודות קצה |
| :--- | :--- | :--- |
| **UserService** | ניהול משתמשים ואימות | `get_user(user_id)` |
| **IndexService** | אינדוקס מסמכים, ניהול מילים ודירוג | `add_document()`, `search_word()`, `score()` |
| **QueryService** | עיבוד וביצוע שאילתות לוגיות (AND/OR) | `create_query()` |
| **ResultService** | עיצוב, פירמוט ומיון תוצאות החיפוש | `format_results()` |
| **IndexerFunction** | אינדוקס מסמכים בסביבת FaaS מבוססת אירועים | `handle(event)` |
| **SearcherFunction** | חיפוש עם דירוג מובנה בסביבת FaaS | `handle(event)` |

---

## 4. דוגמאות לשימוש (פנים קוד)[cite: 1]

### Microservices — שאילתת AND[cite: 1]
```python
query = query_service.create_query({'terms': ['cloud', 'computing'], 'operator': 'AND'})
results = result_service.format_results(query['id'])
# מחזיר רק מסמכים שמכילים את שתי המילים, ממוינים לפי score
Microservices — שאילתת OR
[cite: 1]
Python
query = query_service.create_query({'terms': ['python', 'microservices'], 'operator': 'OR'})
results = result_service.format_results(query['id'])
# מחזיר מסמכים שמכילים לפחות מילה אחת
FaaS (Function as a Service)
[cite: 1]
Python
faas = FaaSSimulator()
faas.invoke('indexer', {'document_id': 'doc1', 'content': 'Python cloud computing'})
faas.invoke('searcher', {'query': 'cloud computing', 'operator': 'AND'})
5. פלטי המערכת (Outputs)
[cite: 1]
פלטי ריצה - Microservices
[cite: 1]
JSON
Added documents: 1, 2

Query results: {
  "id": "1",
  "terms": ["cloud", "computing"],
  "operator": "AND",
  "results": ["1", "2"],
  "timestamp": "now"
}

Formatted results: {
  "id": "1",
  "query_id": "1",
  "formatted_results": [
    {
      "doc_id": "1",
      "title": "Python Programming",
      "snippet": "Python is a popular programming language for cloud computing...",
      "score": 2
    },
    {
      "doc_id": "2",
      "title": "Cloud Services",
      "snippet": "Cloud computing enables scalable microservices architecture...",
      "score": 2
    }
  ],
  "count": 2
}
פלטי ריצה - FaaS הסימולטור
[cite: 1]
1. Event-Driven Invocation
[cite: 1]
Indexed document doc1: {'status': 'success', 'indexed_words': 9}
Indexed document doc2: {'status': 'success', 'indexed_words': 6}
2. Independent Function Calls
[cite: 1]
Search results for 'cloud computing': {'status': 'success', 'operator': 'AND', 'results': [{'doc_id': 'doc1', 'score': 2}, {'doc_id': 'doc2', 'score': 2}]}
Search results for 'python programming': {'status': 'success', 'operator': 'AND', 'results': [{'doc_id': 'doc1', 'score': 2}]}
3. Scalability Demonstration
[cite: 1]
Total function invocations: 4
In real FaaS: These would execute in parallel with automatic scaling