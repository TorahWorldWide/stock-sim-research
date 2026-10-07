# מחקר: נתוני שוק וסימולטורי מסחר — לסימולטור האישי

נבדקו 35 מקורות נתונים (ספקים עם מפתח, ברוקרים, מקורות ללא מפתח, ת"א/אירופה/אסיה ואופציות), 13 פלטפורמות דמו קיימות, 8 סימולטורים בקוד פתוח ו־6 משפחות של נוסחאות לספרד והחלקה. הבדיקות החיות של העובדים נעשו ב־2026-10-07 בין 16:40 ל־17:30 UTC (השוק בארה"ב פתוח, ת"א/אירופה/אסיה סגורות), ושלוש בדיקות החזרה של העורך נעשו ב־17:02–17:04 UTC.
מקרא: ✅ נבדק ועובד היום עם curl · 📄 נקרא בלבד (אין מפתח/חשבון, או שהדף נקרא בלי הרצה) · ❌ נכשל היום · **(אומת היום)** = נבדק באתר הרשמי או ב־curl · **(לא אומת)** = מקור משני, זיכרון או דף שלא נפתח.
הערת יושר: כל ששת החלקים התקבלו ומלאים. פערים שנשארו מסומנים במפורש בכל סעיף ובנספח, ולא הושלמו בניחוש. היכן שעובדים סתרו זה את זה, שתי הגרסאות מופיעות עם הכרעה או עם סימון "לא הוכרע".

## תקציר והמלצות

**מקור מומלץ לגרסה ראשונה בחינם: Yahoo Finance chart v8 (לא רשמי), דרך שרת/proxy קטן.**
- **שלוש סיבות:** (1) זה המקור היחיד שעבר היום ארבע בדיקות חיות בלתי תלויות (A3, A4 ובדיקת החזרה של העורך ב־17:02:28Z), והוא החזיר ל־AAPL נר דקה שחותמת הזמן שלו זהה לשנייה של הבקשה (אומת היום). (2) כיסוי רחב במקור אחד: ארה"ב, ת"א (`TEVA.TA`), לונדון, Xetra, פריז, טוקיו, הונג־קונג; היסטוריה יומית של 20+ שנה (`range=5y` החזיר 1,255 נרות), נרות דקה ל־30 יום, נרות 5 דקות ל־60 יום, רשימת עולות/יורדות (screener) ושרשרת אופציות (v7) (אומת היום). (3) בלי מפתח, בלי הרשמה, בלי כרטיס אשראי, ובלי הגבלה על תושבי ישראל.
- **הסיכון הגדול:** תנאי השימוש של Yahoo אוסרים במפורש איסוף אוטומטי ("robots, spiders, scrapers … without our express, prior permission", [Yahoo ToS](https://legal.yahoo.com/us/en/yahoo/terms/otos/index.html), אומת היום). זה API לא מתועד שיכול להישבר או להיחסם בלי הודעה (היום Stooq נחסם לגמרי, ו־A4 קיבל 429 מ־`query1`). לכן חובה שכבת גיבוי.

**מקור גיבוי: Alpaca Market Data (תוכנית Basic, חשבון Paper Only).** רשמי, עם תנאי שימוש שמתירים "personal and non-commercial" ([ToS PDF](https://files.alpaca.markets/disclosures/library/TermsAndConditions.pdf)), הרשמה באימייל בלבד "Anyone globally" ([Paper Trading](https://docs.alpaca.markets/docs/paper-trading)), 200 קריאות לדקה, היסטוריה מ־2016 כולל נרות דקה, bid/ask (של IEX בלבד) ורשימת movers ([alpaca.markets/data](https://alpaca.markets/data)) (אומת היום בתיעוד). מצב בדיקה: 📄 נקרא בלבד, כי אין מפתח demo. החיסרון: זמן אמת רק מבורסת IEX (כ־2.5% מהנפח), ונתוני SIP מלאים באיחור 15 דק'.

**מקור בתשלום נמוך: Massive (לשעבר Polygon.io) Stocks Starter, ‎$29 לחודש** ([pricing](https://massive.com/pricing), אומת היום). מוסיף: קריאות ללא הגבלה, 5 שנות היסטוריה כולל נרות דקה, WebSocket, איחור 15 דק' במקום סוף יום. לא מוסיף זמן אמת ולא bid/ask. אם נחוץ bid/ask מאוחד בזמן אמת (מה שבאמת מדמה ביצוע גרוע במניות קטנות), אף תוכנית מתחת ל־‎$99 לחודש (Alpaca Algo Trader Plus, SIP מלא + OPRA) לא נותנת את זה (אומת היום).

**5 עובדות שבונה חייב לדעת:**
1. **"זמן אמת" בחינם הוא כמעט תמיד בורסה אחת, לא SIP מאוחד.** Yahoo, Nasdaq.com ו־CNBC מחזירים "Nasdaq Last Sale"; Alpaca מחזירה IEX; Twelve Data החזירה נפח של 582K מול 13.9M במקור מאוחד באותו רגע (אומת היום). לנפח ולנזילות צריך מקור מאוחד (EODHD, Nasdaq.com, Cboe, dxFeed), גם אם הוא מאחר 15 דק'.
2. **כמעט כל המקורות דורשים שרת קטן (proxy).** אין כותרת CORS ב־Yahoo, Nasdaq.com, Cboe, Google, Finviz, Tiingo, EODHD, `api.tase.co.il`, Börse Frankfurt. יש CORS פתוח רק ב־Alpha Vantage, Twelve Data, Finnhub, FMP, Marketstack, Massive, Alpaca, Tradier, dxFeed demo, CNBC ו־TradingView (אומת היום). וגם כשיש CORS, מפתח סודי בקוד הדפדפן נחשף.
3. **מלכודת תנאי שימוש:** כל הספקים עם מפתח מגבילים ל"שימוש אישי/פנימי" ואוסרים להציג או להפיץ את הנתונים. ברגע שהסימולטור נפתח לחברים, זה מפר את התנאים (Alpha Vantage, Twelve Data, Finnhub, EODHD, Tiingo, Alpaca, Massive, אומת היום). TradingView מתירה "display-only" בלבד, כלומר שימוש מתוכנת ב־scanner כנראה אסור.
4. **מגבלות שמכשילות סימולטור:** Alpha Vantage 25 קריאות ביום (סוף יום בלבד בחינם); Marketstack 100 בחודש; EODHD 20 ביום; Massive 5 לדקה; Twelve Data 800 ביום (מספיק לכ־10 מניות בעדכון של דקה); Yahoo מחזיר 429 מיד כשאין User-Agent. היסטוריה של 5 שנים אין בחינם ב־EODHD, Marketstack (שנה אחת) ו־Massive (שנתיים).
5. **אופציות בחינם:** Cboe delayed JSON הוא המקור היחיד ללא מפתח עם שרשרת מלאה, bid/ask עם גודל, OI, IV ו־Greeks (3,568 חוזים ל־AAPL), באיחור 15 דק' ובלי היסטוריה (אומת היום). להיסטוריה: Alpha Vantage `HISTORICAL_OPTIONS` (סוף יום, 25 ביום) או קבצי optionsDX. אופציות בזמן אמת בחינם לא נמצאו.

**הערות הכרעה של העורך (איפה העובדים לא הסכימו):**
- A1 המליץ על Twelve Data, A2 על Alpaca, A3 על Yahoo + Nasdaq.com, A4 על Yahoo. ההמלצה כאן היא Yahoo לגרסה ראשונה בגלל כיסוי ובדיקות חיות, ו־Alpaca כגיבוי משפטי ועצמאי. api.nasdaq.com נשאר המקור החינמי הטוב ביותר ל־bid/ask אמיתי במניות ארה"ב (ספרד של 5 סנט ב־AAPL, אומת היום).
- A3 כתב ש־`range=max` מחזיר נתונים יומיים מ־1984; A4 כתב שהרזולוציה יורדת אוטומטית. בדיקת העורך (17:03:42Z): `interval=1d&range=max` החזיר `dataGranularity: 3mo`, 169 נרות. **A4 צודק.** לנתונים יומיים ארוכים צריך `period1/period2` או `range=5y`/`10y`.
- A4 דיווח ש־`query1` החזיר 429 בקריאה הראשונה; A3 ובדיקת העורך קיבלו 200 מ־`query1`. כנראה חסימה זמנית לפי IP. **לא הוכרע**, ולכן כדאי לתמוך בשני ה־hosts.
- A3 מדד `exchangeDataDelayedBy: 15` ל־TEVA.TA, בעוד טבלת העזרה של Yahoo אומרת 20 דק' לת"א ([SLN2310](https://help.yahoo.com/kb/SLN2310.html)). שני הנתונים מופיעים, **לא הוכרע**.

## חלק א — מקורות נתוני שוק

### טבלת השוואה ראשית

| מקור | איחור | מגבלות חינם | בורסות | מפתח? | פוסלים | ישראל | מהדפדפן? | תנאי שימוש אישי | תוכנית זולה | בדיקה חיה |
|---|---|---|---|---|---|---|---|---|---|---|
| **Alpha Vantage** (A1, A4) | סוף יום (יום המסחר הקודם); זמן אמת/15 דק' רק בתשלום | 25 בקשות ביום | ארה"ב + גלובלי (לא אומת) | כן, אימייל | לא נמצאו | לא נמצאה הגבלה | ✅ `*` | אישי, לא מסחרי | ‎$49.99/חודש, 75 לדקה | ✅ IBM עם `demo` (גם בדיקת העורך 17:02:31Z) |
| **Finnhub** (A1) | "realtime" לארה"ב לפי האתר (לא אומת) | 60 לדקה (לא אומת, ללא קישור) | ארה"ב בחינם | כן | לא אומת | לא נמצאה הגבלה | ✅ `*` | "strictly for personal use" | לא אומת (JS) | 📄 אין demo (401) |
| **Twelve Data** (A1) | זמן אמת לארה"ב, נמדד כדקה | 8 קרדיטים לדקה, 800 ביום | ארה"ב, פורקס, קריפטו | כן, בלי כרטיס | אין | לא נמצאה הגבלה | ✅ `*` | Internal Use, אסור מסחרי | ‎$79/חודש (Grow) | ✅ AAPL 336.11 |
| **Massive (Polygon.io)** (A1, A4) | סוף יום; Starter 15 דק' | 5 לדקה; שנתיים היסטוריה | ארה"ב | כן | לא אומת | לא באמברגו → מותר | ✅ משקף Origin | אישי, לא מסחרי | ‎$29/חודש Starter (מניות או אופציות) | 📄 אין demo (401) |
| **FMP** (A1) | סוף יום (לא אומת, 403) | 250 ביום (לא אומת) | בעיקר ארה"ב | כן | בלי כרטיס (לא אומת) | לא אומת | ✅ `*` | לא אומת (403) | לא אומת | ❌ `demo` נדחה 401 |
| **Tiingo** (A1) | סוף יום; IEX רק ב־Power | 50 לשעה, 1,000 ביום, 500 סימבולים בחודש | ארה"ב | כן | לא אומת | לא נמצאה הגבלה | ❌ אין CORS | "Internal Use Only" | ‎$30/חודש Power | 📄 אין demo (403) |
| **EODHD** (A1) | 15–20 דק' (נמדד 15) | 20 ביום (+500 בונוס), 20 לדקה; שנה היסטוריה | 70+ בורסות (TASE לא אומת) | כן | לא אומת | לא נמצאה הגבלה | ⚠️ אין `allow-origin` | פרטי, לא מסחרי, אסור להציג | ‎£19.99/חודש (EOD בלבד) | ✅ AAPL 335.42 |
| **Marketstack** (A1) | סוף יום | 100 בחודש; שנה היסטוריה | 2,700+ בורסות | כן | לא אומת | לא אומת | ✅ `*` | לא אומת | ‎$9.99/חודש | 📄 אין demo (401) |
| **Alpaca Basic** (A2) | IEX בזמן אמת; SIP באיחור 15 דק' | 200 לדקה; WS עד 30 סימבולים | ארה"ב | כן, אימייל (Paper Only) | אין | "Anyone globally" | ✅ `*` (מפתח נחשף) | אישי, לא מסחרי | ‎$99/חודש Algo Trader Plus | 📄 401 בלי מפתח |
| **Tradier Sandbox** (A2, A4) | 15 דק' | 60 לדקה | ארה"ב (מניות+אופציות) | כן, token | ⚠️ כנראה חשבון ברוקראז' (KYC) | לא אומת | ✅ `*` | לא אומת | Pro ‎$10/חודש (מסלול עמלות) | 📄 401 |
| **Interactive Brokers API** (A2) | Cboe One+IEX בזמן אמת ללקוח ממומן | אין חינמי | גלובלי כולל ת"א | חשבון ממומן | ❌ חשבון ממומן + KYC | לא אומת | ❌ Gateway מקומי | Non-Pro אישי (לא אומת) | ‎$10/חודש + מינימום ‎$500 בחשבון | 📄 |
| **Charles Schwab API** (A2) | זמן אמת ללקוחות (לא אומת) | לא אומת | ארה"ב | חשבון + אישור אפליקציה | ❌ חשבון ברוקראז' | לא אומת | OAuth → שרת | — | חינם ללקוחות | 📄 401 |
| **Webull OpenAPI** (A2) | לפי מנוי בתשלום | לא אומת | ארה"ב ועוד | חשבון + בקשת API | ❌ חשבון, אישור 1–2 ימים, נתונים בתשלום | לא אומת | ❌ HMAC → שרת | לא אומת | לא אומת | 📄 |
| **moomoo / Futu OpenD** (A2) | US LV3 "בתקופת מבצע" | 60 ל־30 שנ'; 100 מנויים + 100 סימבולי היסטוריה לשבוע | ארה"ב, HK, A-shares | חשבון moomoo + OpenD מקומי | ⚠️ שאלון, הסכמים, תוכנה מקומית | ❓ ישראל לא ברשימה (לא אומת) | ❌ TCP מקומי | לא נבדק | — | 📄 |
| **Databento** (A2) | זמן אמת רק במנוי | ‎$125 קרדיט חד־פעמי | ארה"ב, עתידים, אופציות | כן | ❌ חובה כרטיס אשראי | לא אומת | ⚠️ אין ACAO | לא אומת | לא אומת (JS) | 📄 401 |
| **dxFeed demo** (A2) | ~15 דק' (נמדד) | לא פורסמו; ≤8,000 נרות לבקשה | ארה"ב נבדקה | לא | אין | לא נמצאה הגבלה | ✅ `*` | אין תנאים ל־demo; "for testing" | הצעת מחיר בלבד | ✅ AAPL 335.70 + bid/ask |
| **Intrinio** (A2) | Sandbox מוגבל | ניסיון 14 יום | ארה"ב | כן | ~‎$150/חודש (לא אומת) | לא אומת | ✅ `*` | לא אומת | ~‎$150 (לא אומת) | 📄 401 |
| **Barchart OnDemand** (A2) | לא אומת | לא אומת | ארה"ב | כן | דף לא נטען | לא אומת | לא נבדק | לא אומת | לא אומת | 📄 401 |
| **Yahoo Finance chart v8** (A3, A4, עורך) | NASDAQ ~1–2 שנ'; TASE/LSE 20 דק' (נמדד 15 ל־TEVA.TA); Xetra/Paris/HK 15; טוקיו 20 | לא פורסמו; 30 רצופות עברו; 429 בלי UA; A4: 429 מ־query1 | ארה"ב, TASE, אירופה, אסיה | לא | אין | לא נמצאה הגבלה | ❌ אין ACAO | אוסר איסוף אוטומטי | אין API בתשלום | ✅ (3 עובדים + העורך 17:02:28Z) |
| **Yahoo options v7** (A4, A3) | ~15 דק' (OPRA 15 דק' בטבלת Yahoo) | לא פורסמו; crumb+cookie | אופציות ארה"ב | crumb (בלי חשבון) | אין | לא נמצאה הגבלה | ❌ | כמו Yahoo | — | ✅ 23 פקיעות |
| **Yahoo screener** (A3, A4) | קרוב לזמן אמת (לא נמדד) | לא פורסמו | ארה"ב | crumb (A3: עבד גם בלי) | אין | לא נמצאה הגבלה | ❌ | כמו Yahoo | — | ✅ day_gainers |
| **Stooq** (A3, A4, עורך) | — | — | — | לא | — | — | — | לא אומת | — | ❌ 404 + אתגר JS (גם העורך 17:02:30Z) |
| **Nasdaq.com `api.nasdaq.com`** (A3) | <1 דק' (`isRealTime:true`, Nasdaq Last Sale) | לא פורסמו; 30 רצופות עברו; חובה UA | ארה"ב | לא | אין | לא נמצאה הגבלה | ❌ אין ACAO | "personal, non-commercial" | אין | ✅ bid/ask 336.11/336.16 |
| **Cboe delayed JSON** (A3, A4) | ~15 דק' (נמדד 15.7) | לא פורסמו; CDN s-maxage=5 | ארה"ב, מניות+אופציות | לא | אין | לא נמצאה הגבלה | ❌ אין ACAO | "personal non-commercial use" | LiveVol (לא נבדק) | ✅ 3,568 חוזים |
| **Cboe All Access API** (A4) | תלוי במנויי SIP | ניסיון 500 נק'/יום ל־14 יום | ארה"ב | חשבון | ❌ כרטיס אשראי | לא אומת | — | — | ‎$599/חודש Tier 1 (לא אומת ישירות) | 📄 |
| **TradingView scanner** (A3) | 15 דק' (`delayed_streaming_900`) | לא פורסמו | ארה"ב + עשרות שווקים | לא | אין | לא נמצאה הגבלה | ✅ משקף Origin (body כ־text/plain) | display-only; אסור מסחרי | לא נבדק | ✅ |
| **Finviz** (A3) | ~1 דק' ("delayed by 1 minute") | export רק ב־Elite | ארה"ב | לא (Elite לייצוא) | Elite: כרטיס אשראי | לא נמצאה הגבלה | ❌ scrape HTML | לא אומת (ToS 404) | Elite ‎$39.50/חודש | ✅ scrape |
| **Google Finance** (A3) | מחיר תואם זמן אמת; TASE 20 דק' | אין API | ארה"ב, TASE, אירופה, אסיה | לא | אין | לא נמצאה הגבלה | ❌ | אסור להעתיק/לאחסן/להפיץ | — | ✅ scrape |
| **CNBC `quote.htm`** (A3) | <1 דק' (`realTime:"true"`) | לא פורסמו | ארה"ב (ועוד, לא נבדק) | לא | אין | לא נמצאה הגבלה | ✅ `*` | לא אומת (403) | — | ✅ (`restQuote.htm` ❌ 404) |
| **TASE DataHub (רשמי)** (A4) | לא אומת | 4 מוצרים חינמיים; מכסות לא פורסמו | TASE | כן (פורטל OIDC) | לא ידוע (פורטל סגור) | בורסה ישראלית | לא נבדק | לא אומת | לא אומת | 📄 401 |
| **`api.tase.co.il` (לא רשמי)** (A4) | סוף יום נבדק; תוך־יומי לא נבדק | לא פורסמו; Incapsula | TASE | לא (רק `Referer`) | אין | ישראלי | ❌ 403 עם Origin זר | לא נמצא | — | ✅ TEVA 11990 אג' |
| **Maya (`mayaapi.tase.co.il`)** (A4) | — | — | דיווחי חברות | — | — | — | — | לא נמצא | — | ❌ security violation |
| **Investing.com** (A4) | — | — | — | — | — | — | — | אוסר scraping (לא אומת) | — | ❌ 403 |
| **Börse Frankfurt `api.boerse-frankfurt.de`** (A4) | כמעט בזמן אמת? (לא אומת) | לא פורסמו | Xetra/Frankfurt | לא | אין | לא נמצאה הגבלה | ❌ 403 עם Origin זר | לא נבדק | — | ✅ SAP 188.02 |
| **Euronext live** (A4) | — | — | — | — | — | — | — | — | — | ❌ תגובה מוצפנת |
| **optionsDX** (A4) | קבצי סוף יום | 10 מוצרים ב־$0 | אופציות ארה"ב | כנראה חשבון | לא אומת | לא אומת | — | לא נבדק | חבילות בתשלום | 📄 |

### מטריצת צרכים 1–6

הצרכים: 1 מחיר עדכני · 2 היסטוריה ≥5 שנים · 3 נפח + bid/ask · 4 כיסוי בורסות · 5 עולות/יורדות · 6 אופציות.

| מקור | 1. מחיר עדכני | 2. היסטוריה ≥5 שנים | 3. נפח + bid/ask | 4. כיסוי | 5. עולות/יורדות | 6. אופציות |
|---|---|---|---|---|---|---|
| Alpha Vantage | ❌ סוף יום | ✅ 20+ שנה יומי (6,773 ימים ל־IBM); תוך־יומי בתשלום | חלקי: נפח; bid/ask בתשלום | ✅ גלובלי | ✅ `TOP_GAINERS_LOSERS` (מלא במניות פני) | ✅ `HISTORICAL_OPTIONS` סוף יום עם Greeks |
| Finnhub | ✅ זמן אמת (לא אומת) | חלקי: נרות בתשלום (לא אומת) | חלקי: נפח, בלי bid/ask | חלקי: ארה"ב | ❌ | ❌ בתשלום |
| Twelve Data | ✅ כדקה | ✅ יומי מ־2006, 1-min | חלקי: נפח חשוד כלא מאוחד; בלי bid/ask | חלקי: ארה"ב | ❌ בתשלום (לא אומת) | ❌ |
| Massive | ❌ סוף יום | חלקי: שנתיים (כולל minute) | חלקי: נפח; bid/ask בתשלום | חלקי: ארה"ב | ❌ בתשלום (לא אומת) | חלקי: Options Basic סוף יום, 5 לדקה, בלי Greeks/quotes |
| FMP | ❌ סוף יום | ✅ 5 שנים (לא אומת) | חלקי | חלקי | לא אומת | ❌ |
| Tiingo | ❌ סוף יום | ✅ 30+ שנה | חלקי: bid/ask IEX רק ב־Power | חלקי | ❌ | ❌ |
| EODHD | חלקי: 15 דק' | ❌ שנה אחת בחינם | חלקי: נפח מאוחד, בלי bid/ask | ✅ גלובלי | ❌ | ❌ בתשלום |
| Marketstack | ❌ סוף יום | ❌ שנה אחת | חלקי: נפח | ✅ | ❌ | ❌ |
| Alpaca Basic | חלקי: IEX בזמן אמת | ✅ מ־2016 כולל 1-min | חלקי: bid/ask IEX; נפח SIP באיחור | חלקי: ארה"ב | ✅ `movers` (לא אומת אם פתוח ל־Basic) | חלקי: indicative feed |
| Tradier Sandbox | ❌ 15 דק' | ✅ יומי לכל חיי המניה; 1-min ל־20 יום | ✅ | חלקי: ארה"ב | ❌ | ✅ שרשראות באיחור |
| IBKR | ✅ (חשבון ממומן) | ✅ | ✅ | ✅ גלובלי כולל ת"א | חלקי: סורקים | בתשלום |
| Schwab | ✅ (ללקוחות) | ✅ | ✅ | חלקי | ✅ movers | ✅ |
| Webull | בתשלום | לא אומת | לא אומת | חלקי | לא אומת | בתשלום |
| moomoo OpenD | ✅ (מבצע) | חלקי: 100 סימבולים לשבוע | ✅ | חלקי: ארה"ב, HK, סין | לא אומת | חלקי |
| Databento | בתשלום | ✅ בתשלום | ✅ tick + ספר פקודות | חלקי: ארה"ב | ❌ | בתשלום |
| dxFeed demo | חלקי: 15 דק' | ✅ יומי מ־1994; 1-min ~2 שבועות | ✅ bid/ask + נפח יומי | חלקי: ארה"ב | ❌ | לא נבדק |
| Intrinio | חלקי: sandbox | לא אומת | לא אומת | ארה"ב | לא אומת | בתשלום |
| Barchart | לא אומת | לא אומת | לא אומת | לא אומת | לא אומת | לא אומת |
| Yahoo chart v8 | ✅ זמן אמת (Nasdaq) / 15–20 דק' בחו"ל | ✅ מ־1984 (AAPL), TEVA.TA מ־2002; 1m ל־30 יום, 5m ל־60 יום | חלקי: נפח כן; bid/ask (v7) לא אמין: 333.12/339.32 מול 336.02 | ✅ כולל TASE | ✅ screener | ✅ v7 |
| Yahoo options v7 | — | ❌ | ✅ bid/ask/OI/vol/IV, בלי Greeks | חלקי: ארה"ב | — | ✅ |
| Stooq | ❌ חסום | ❌ | ❌ | ❓ | ❌ | ❌ |
| Nasdaq.com | ✅ <1 דק' | ✅ 10 שנים (2,513 שורות) | ✅ נפח + bid/ask + size | חלקי: ארה"ב | ✅ marketmovers כולל מניות פני | חלקי (לא נבדק) |
| Cboe delayed JSON | חלקי: 15 דק' | ❌ | ✅ bid/ask + size | חלקי: ארה"ב | ❌ | ✅ שרשרת מלאה + Greeks |
| Cboe All Access | נפסל (כרטיס אשראי) | נפסל | נפסל | נפסל | נפסל | נפסל |
| TradingView scanner | חלקי: 15 דק' | ❌ | חלקי: bid/ask = null | ✅ רב־שווקי | ✅ מיון (צריך סינון OTC) | ❌ |
| Finviz | ✅ ~1 דק' | ❌ export בתשלום | חלקי: נפח בלבד | חלקי: ארה"ב | ✅ `ta_topgainers` | ❌ |
| Google Finance | ✅ | ❌ | חלקי | ✅ | חלקי (scrape) | ❌ |
| CNBC | ✅ | ❌ (לא נבדק) | חלקי: נפח, בלי bid/ask | חלקי | ❌ | ❌ |
| TASE DataHub | ? לא נבדק | ? | ? | חלקי: TASE | ? | ❌ |
| `api.tase.co.il` | חלקי: סוף יום | ✅ EOD מ־2000 (לפי TASE) | חלקי: מחזור כן, bid/ask לא נמצא | חלקי: TASE | ? | ❌ |
| Maya | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Investing.com | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Börse Frankfurt | ✅? | ? | חלקי: מחזור | חלקי: גרמניה | ? | ❌ |
| Euronext live | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| optionsDX | ❌ | חלקי: קבצים | ? | חלקי | ❌ | חלקי: EOD |

### פירוט לכל מקור

#### ספקים רשמיים עם מפתח (A1)

**1. Alpha Vantage** — https://www.alphavantage.co
- **איחור:** בחינם סוף יום בלבד. `TIME_SERIES_INTRADAY` מסומן Premium: "If you would like to access realtime, 15-minute delayed, and/or historical intraday data, please subscribe to a premium membership plan" ([documentation](https://www.alphavantage.co/documentation/)) (אומת היום). `GLOBAL_QUOTE` החזיר `latest trading day: 2026-10-06` בזמן שהבורסה פתוחה ב־10-07 (A1, וגם בדיקת העורך ב־17:02:31Z: IBM 221.29, אותו יום).
- **מגבלות:** "25 API requests per day" ([support](https://www.alphavantage.co/support/), [premium](https://www.alphavantage.co/premium/)) (אומת היום).
- **היסטוריה:** `TIME_SERIES_DAILY` עם `outputsize=full` ל־IBM: 6,773 ימים מ־1999-11-01 (אומת היום).
- **bid/ask:** בתשלום. **עולות/יורדות:** `TOP_GAINERS_LOSERS` עבד עם demo, הרשימה מלאה במניות פני (`NMCO^` ‎$0.02) (אומת היום). **אופציות:** `HISTORICAL_OPTIONS` עובד עם demo **רק ל־IBM**, מחזיר שרשרת של יום המסחר הקודם עם bid/ask/size/OI/IV/Greeks (2,112 חוזים, 1.4MB); `REALTIME_OPTIONS` בתשלום (600 בקשות לדקה ומעלה) (A1+A4, אומת היום). פקודה: `curl -s 'https://www.alphavantage.co/query?function=HISTORICAL_OPTIONS&symbol=IBM&apikey=demo'` → 200, 1.4MB.
- **מפתח:** טופס אימייל ([support#api-key](https://www.alphavantage.co/support/#api-key)). **פוסלים:** לא נמצאו. **ישראל:** לא נמצאה הגבלה.
- **CORS:** `access-control-allow-origin: *` (אומת היום).
- **ToS** ([PDF](https://www.alphavantage.co/terms_of_service/)): "for personal, non-commercial use, unless you and Alpha Vantage have agreed otherwise in writing" (אומת היום).
- **תוכנית זולה:** ‎$49.99/חודש, 75 לדקה; זמן אמת דורש entitlement ([premium](https://www.alphavantage.co/premium/)) (אומת היום).
- **בדיקה חיה:** ✅ (demo עובד על IBM בלבד; AAPL מחזיר "demo purposes only").
```
curl "https://www.alphavantage.co/query?function=GLOBAL_QUOTE&symbol=IBM&apikey=demo"
→ "05. price": "221.2900", "06. volume": "3938659", "07. latest trading day": "2026-10-06"   (A1 ~16:55Z; העורך 17:02:31Z זהה)
```

**2. Finnhub** — https://finnhub.io
- **איחור:** האתר: "Free realtime APIs for stock, forex and cryptocurrency" ([pricing](https://finnhub.io/pricing)). זמן אמת למניות ארה"ב בחינם לפי תוצאת חיפוש (לא אומת, דף התמחור ב־JS).
- **מגבלות:** 60 לדקה לפי תוצאת חיפוש (**לא אומת, ללא קישור**).
- **demo:** אין. `sandbox_c0` החזיר `401 Invalid API key` (אומת היום). נרות, אופציות ו־bid/ask: Premium לפי זיכרון העובד (לא אומת).
- **CORS:** `*` (אומת היום). **ToS** ([terms](https://finnhub.io/terms-of-service)): "All plan listed on Finnhub website is strictly for personal use", "agree to not redistribute or share access to data" (אומת היום).
- **תוכנית זולה:** לא אומת. **בדיקה חיה:** 📄.
```
curl "https://finnhub.io/api/v1/quote?symbol=AAPL&token=sandbox_c0" → HTTP 401 {"error":"Invalid API key."}
```

**3. Twelve Data** — https://twelvedata.com
- **איחור:** Basic: "US equities: Real-time" ([pricing](https://twelvedata.com/pricing)). נמדד: נר דקה אחרון `12:51:00 ET` נשלף ב־12:52 ET, `last_quote_at` = 16:52 UTC (אומת היום).
- **⚠️ נפח:** 582,479 מול 13,885,872 ב־EODHD באותו רגע. כנראה בורסה אחת ולא SIP (לא אומת בתיעוד). לא להשתמש בנפח הזה לנזילות.
- **מגבלות:** 8 קרדיטים לדקה, 800 ביום (אומת היום). **היסטוריה:** `time_series` יומי עם `outputsize=5000` מ־2006-11-20; 1-min עובד עם demo (אומת היום).
- **bid/ask:** אין. **עולות/יורדות:** `market_movers` בתוכניות גבוהות (לא אומת). **אופציות:** לא בחינם (לא אומת).
- **מפתח:** "No credit card required" (אומת היום). **ישראל:** רק "export … to prohibited countries" (סעיף 13.3).
- **CORS:** `*`. **ToS** ([terms](https://twelvedata.com/terms)): "solely for Internal Use" (2.1), אסור "Use Free Tier data for commercial purposes" (2.3(l)), הפצה רק עם Redistribution Add-On (2.2(e)) (אומת היום).
- **תוכנית זולה:** Grow ‎$79/חודש (‎$66 שנתי), 20+ שווקים (אומת היום). **בדיקה חיה:** ✅.
```
curl "https://api.twelvedata.com/price?symbol=AAPL&apikey=demo"  → {"price":"336.11"}   (16:52 UTC)
curl "https://api.twelvedata.com/quote?symbol=AAPL&apikey=demo"
→ "close":"336.04","volume":"582479","previous_close":"333.63000","last_quote_at":1791391920,"is_market_open":true
```

**4. Massive (לשעבר Polygon.io)** — https://massive.com
- **מותג:** Polygon.io שינה שם ל־Massive ב־30.10.2025 ([einpresswire](https://www.einpresswire.com/article/863823068/polygon-io-is-now-massive), [FISD](https://fisd.net/polygon-io-is-now-massive)) (לא אומת באתר הרשמי). `api.polygon.io` ו־`api.massive.com` עונים זהה (אומת היום).
- **מניות:** Stocks Basic חינמי: "End of day data", 5 לדקה, שנתיים היסטוריה כולל minute aggregates; Starter ‎$29/חודש: 15 דק' איחור, קריאות ללא הגבלה, 5 שנים, WebSocket ([pricing](https://massive.com/pricing)) (אומת היום).
- **אופציות** (A4, [pricing?product=options](https://massive.com/pricing?product=options), אומת היום): Options Basic $0: `"5 API Calls / Minute"`, `"End of Day Data"`, `"2 Years"`, `"greeks":"unavailable"`, `"quotes":"unavailable"`; Options Starter ‎$29/חודש: ללא הגבלת קריאות, 15 דק', Greeks, snapshot, בלי quotes; Developer ‎$79.
- **ישראל:** ToS דורשים "not located in a country that is subject to a U.S. Government embargo" ([individuals ToS](https://massive.com/legal/individuals-terms-of-service)). ישראל לא ביניהן.
- **CORS:** משקף Origin (אומת היום). **ToS:** "solely for your own personal, non-commercial, and non-business purposes" (אומת היום).
- **בדיקה חיה:** 📄 אין demo.
```
curl "https://api.massive.com/v2/aggs/ticker/AAPL/prev?apiKey=demo" → HTTP 401 {"status":"ERROR","error":"Unknown API Key"}
curl "https://api.massive.com/v3/snapshot/options/AAPL?apiKey=DEMO" → 401 (A4)
```

**5. Financial Modeling Prep (FMP)** — https://site.financialmodelingprep.com
- Basic: "End of Day Historical Data", "250 Calls / Day", 5 שנים ([pricing-plans](https://site.financialmodelingprep.com/pricing-plans), [FAQ](https://site.financialmodelingprep.com/faqs)) — **לא אומת**: האתר החזיר 403 ל־curl ול־WebFetch.
- "no credit card to sign up" ([מאמר](https://site.financialmodelingprep.com/education/other/do-you-need-a-credit-card-to-use-financial-modeling-prep)) (לא אומת). ToS, ישראל, תוכנית זולה: לא אומת (403).
- **CORS:** `*` (אומת היום). **בדיקה חיה:** ❌.
```
curl "https://financialmodelingprep.com/stable/quote?symbol=AAPL&apikey=demo" → HTTP 401 {"Error Message":"Invalid API KEY. ..."}
```

**6. Tiingo** — https://www.tiingo.com
- **איחור:** Starter חינמי = סוף יום; פיד IEX רק ב־Power ([pricing](https://www.tiingo.com/about/pricing)) (אומת היום). הערה: בעבר IEX היה גם בחינם, וזה לא מה שמוצג היום.
- **מגבלות:** 50 לשעה, 1,000 ביום, 500 סימבולים בחודש, 1GB (אומת היום). **היסטוריה:** "30+ Years" EOD.
- **CORS:** ❌ אין כותרות (אומת היום). **ToS** (מדף התמחור): "Internal use means you may only use the data for your own personal use and you may not display or share the data". הקישור הנכון לתנאים: [tiingo.com/tos](https://www.tiingo.com/tos) (`/about/terms` החזיר 404).
- **תוכנית זולה:** Power ‎$30/חודש לאדם פרטי: 10,000 לשעה, פיד IEX עם bid/ask של IEX (אומת היום). **בדיקה חיה:** 📄.
```
curl "https://api.tiingo.com/iex/aapl?token=demo" → HTTP 403 {"detail":"Invalid token."}
```

**7. EODHD** — https://eodhd.com
- **איחור:** "Prices are delayed: 15-20 minutes for stocks" ([live API](https://eodhd.com/financial-apis/live-realtime-stocks-api)). נמדד: בקשה ב־16:52 UTC החזירה `timestamp` 16:37 UTC (אומת היום).
- **מגבלות:** 20 ביום (+500 בונוס), 20 לדקה ([pricing](https://eodhd.com/pricing)). היסטוריה בחינם: שנה. demo על AAPL החזיר 11,546 ימים מ־1980, אבל demo ≠ חינמי (אומת היום).
- **נפח:** 13,885,872, מאוחד. **bid/ask:** אין. **TASE:** לא אומת.
- **CORS:** ⚠️ יש `allow-credentials/methods/headers` אבל **אין `allow-origin`** → דפדפן יחסום (אומת היום).
- **ToS** ([terms](https://eodhd.com/financial-apis/terms-conditions)): "store, manipulate, and analyze the data for private, non-commercial purposes"; אסור "Selling, reselling, retransmitting, redistributing, displaying" (אומת היום).
- **תוכנית זולה:** Historian ‎£19.99/חודש, EOD בלבד (אומת היום). **בדיקה חיה:** ✅.
```
curl "https://eodhd.com/api/real-time/AAPL.US?api_token=demo&fmt=json"
→ {"code":"AAPL.US","timestamp":1791391020,"close":335.42,"volume":13885872,"previousClose":333.63}
```

**8. Marketstack** — https://marketstack.com
- בחינם: סוף יום, **100 בקשות לחודש**, שנה היסטוריה; 2,700+ בורסות ([pricing](https://marketstack.com/pricing)) (אומת היום).
- ToS מפנים ל־[ideracorp.com/legal/APILayer](https://www.ideracorp.com/legal/APILayer), לא נקראו. **CORS:** `*`.
- **תוכנית זולה:** Basic ‎$9.99/חודש: 10,000 בחודש, תוך־יומי IEX, 10 שנים (אומת היום). **בדיקה חיה:** 📄.
```
curl "https://api.marketstack.com/v2/eod/latest?access_key=demo&symbols=AAPL" → HTTP 401 invalid_access_key
```

#### ברוקרים ושירותים דמויי־ברוקר (A2)

**9. Alpaca Markets — Market Data API (Basic)**
- **איחור:** real-time **רק מ־IEX**; SIP בחינם רק באיחור: "Historical data limitation: latest 15 minutes" ([About Market Data API](https://docs.alpaca.markets/docs/about-market-data-api), [Historical Stock Data](https://docs.alpaca.markets/docs/historical-stock-data-1): "iex … This is the only feed that can be used without a subscription"). דף התמחור: "Real-time: Yes, via websocket / 15 minute delay via API" ([alpaca.markets/data](https://alpaca.markets/data)) (אומת היום).
- **מגבלות:** 200 לדקה; WebSocket עד 30 סימבולים; לא נמצאה מגבלה יומית (אומת היום). **היסטוריה:** "Since 2016" כולל 1-min.
- **bid/ask:** `quotes/latest` ו־snapshots, IEX בלבד. **עולות/יורדות:** `GET /v1beta1/screener/{market_type}/movers?top=N` "based on real time SIP data" ([movers](https://docs.alpaca.markets/reference/movers-1)); לא אומת אם פתוח ל־Basic. **אופציות:** "Indicative Pricing Feed" ב־Basic; OPRA רק ב־Algo Trader Plus (אומת היום).
- **הרשמה:** "Anyone globally can create an Alpaca Paper Only Account! All you need to do is sign up with your email address" ([Paper Trading](https://docs.alpaca.markets/docs/paper-trading)) (אומת היום). דף ההרשמה ([signup](https://app.alpaca.markets/signup)) הוא JS (לא אומת). רשימת מדינות לחשבון אמיתי ([Countries](https://alpaca.markets/support/countries-alpaca-is-available)) לא נקראה.
- **CORS:** `Access-Control-Allow-Origin: *` + `Allow-Headers: Apca-Api-Key-Id, Apca-Api-Secret-Key`. **מלכודת:** המפתח הסודי נחשף בדפדפן.
- **ToS** ([PDF](https://files.alpaca.markets/disclosures/library/TermsAndConditions.pdf)): "Personal and Non-Commercial Usage – … solely for your own personal and non-commercial purposes" (אומת היום).
- **תוכנית זולה:** Algo Trader Plus ‎$99/חודש: SIP בזמן אמת, 10,000 לדקה, OPRA (אומת היום). **בדיקה חיה:** 📄.
```
curl -sI -H 'Origin: https://example.com' https://data.alpaca.markets/v2/stocks/AAPL/quotes/latest
HTTP/1.1 401 Unauthorized   Access-Control-Allow-Origin: *   (גם עם מפתח "demo")
```

**10. Tradier — Developer Sandbox**
- **איחור:** "We delay our market data the industry standard 15-minutes for all sandbox data"; אין streaming מושהה ([Market Data](https://docs.tradier.com/docs/market-data.md), [market-data](https://docs.tradier.com/docs/market-data)) (אומת היום, A2+A4).
- **מגבלות:** Sandbox 60 לדקה, Production 120 ([Rate Limiting](https://docs.tradier.com/docs/rate-limiting)). **היסטוריה:** יומי "entire lifetime of the company"; 1min ל־20 יום; 5/15min ל־40 יום; אין tick ב־sandbox ([Historical Data](https://docs.tradier.com/docs/historical-data.md)) (אומת היום).
- **פוסלים ⚠️ (לא הוכרע):** התיעוד: "All Tradier Brokerage account holders can create a paper trading account" ו־"If you are not a Tradier Brokerage account holder, we are unable to provide you with any real-time data solution" (אומת היום). כתבות מ־2014 תיארו sandbox "no brokerage account required" ([VentureBeat](https://venturebeat.com/business/tradier-announces-launch-of-new-free-developer-sandbox-api), [Tradier blog](https://blog.tradier.com/blog/2014/07/free-developer-sandbox-.html)). דף ההרשמה ([developer.tradier.com/user/sign_up](https://developer.tradier.com/user/sign_up), [Getting Started](https://docs.tradier.com/docs/getting-started.md)) הוא JS ולא נקרא (A2, A4). **מסקנת A2: נפסל כחינמי עד שיוכח אחרת.**
- **הערה (A2):** בקוד לדוגמה הופיע `Origin: https://example.com` לבדיקת CORS; ב־IBKR ה־Gateway המקומי רץ על `https://localhost:5000`.
- **ישראל:** "150+ countries" ([support](https://support.tradier.com/are-there-any-requirements-to-open-an-account-at-tradier-brokerage)) (לא אומת). **CORS:** `*`. **תמחור:** Lite $0 / Pro ‎$10 / Pro Plus ‎$35, "API Access: Included" ([Pricing](https://tradier.com/individuals/pricing)) (אומת היום).
- **בדיקה חיה:** 📄. `GET https://sandbox.tradier.com/v1/markets/quotes?symbols=AAPL` → 401 "Invalid access token"; שרשרת אופציות → 401 (A4).

**11. Interactive Brokers — Client Portal Web API / TWS API**
- ❌ **נפסל כחינמי.** "to use the Trader Workstation, Client Portal or any of our APIs, you do need to have a fully funded account … IBKR Pro and a funded account are required" ([IBKR Campus](https://www.interactivebrokers.com/campus/?p=203297)) (אומת היום). "demo accounts cannot subscribe to data" ([Web API Requirements](https://www.interactivebrokers.com/docs/web-api/v1/requirements-limitations/introduction)) (לא אומת).
- ללקוחות: "free real-time streaming market data on all US-listed stocks and ETFs from Cboe One and IEX (Non-consolidated)", delayed "usually 10-20 minutes behind", 100 snapshot בחודש; מנויי נתונים דורשים "Minimum Equity Balance … Individual USD 500.00" ([Market Data Pricing](https://www.interactivebrokers.com/en/pricing/market-data-pricing.php)) (אומת היום).
- **תוכנית זולה:** "US Securities Snapshot and Futures Value Bundle" ‎$10/חודש (מתבטל ב־$30 עמלות); OPRA L1 ‎$1.50 (אומת היום). **CORS:** לא רלוונטי, Gateway מקומי. **בדיקה חיה:** 📄.

**12. Charles Schwab Developer Portal (Trader API – Individual)**
- ❌ **נפסל כחינמי:** חשבון Schwab + thinkorswim, בקשת גישה והמתנה לאישור ([schwabr README](https://cran.nics.utk.edu/cran/web/packages/schwabr/readme/README.html), [Wealth-Lab](https://wealth-lab.com/Discussion/Schwab-broker-extension-Beta-release-11565)) (לא אומת, מקורות משניים). [developer.schwab.com](https://developer.schwab.com/) הוא JS.
- OAuth עם refresh token שפג אחרי 7 ימים (לא אומת) → שרת. **בדיקה חיה:** 📄.
```
curl -sI 'https://api.schwabapi.com/marketdata/v1/quotes?symbols=AAPL' → HTTP/2 401 … error="invalid_token"
```

**13. Webull OpenAPI**
- ❌ **נפסל כחינמי:** "API applications are typically reviewed within 1–2 business days", "Market data subscriptions for OpenAPI are purchased separately" ([Trading API FAQ](https://developer.webull.com/apis/docs/faq)) (אומת היום). דרישת חשבון ברוקראז' ב־[SDK README](https://github.com/webull-inc/webull-openapi-python-sdk) (לא אומת). חתימת HMAC → שרת. **בדיקה חיה:** 📄.

**14. moomoo / Futu OpenAPI (OpenD)**
- "Without the need for account opening, you can log in to OpenD using your Moomoo account", ואז "complete API Questionnaire and Agreements" ([Authorities and Quota](https://openapi.moomoo.com/moomoo-api-doc/en/intro/authority.html)) (אומת היום).
- US: "LV3 market quotes for free during promotion period (Nasdaq Basic + TotalView + NYSE Arcabook)"; אופציות ארה"ב בחינם רק עם נכסים >$0. Snapshot: "60 requests every 30 seconds"; מכסת מנויים/היסטוריה 100/100 לשבוע למשתמש מתחת ל־10,000 HKD (אומת היום).
- **ישראל:** לפי [Available Countries](https://www.moomoo.com/us/learn/moomoo-available-countries) (לא אומת) ישראל לא ברשימה. **CORS:** ❌ TCP מקומי. **בדיקה חיה:** 📄.

**15. Databento**
- "real-time data requires a Standard, Plus, or Unlimited plan"; "Sign up for $125 in free credits" ([pricing](https://databento.com/pricing)) (אומת היום); קרדיט לשימוש היסטורי או חודש ראשון ([credits FAQ](https://databento.com/docs/faqs/usage-pricing-and-data-credits)) (לא אומת).
- ❌ **נפסל כחינמי:** "Credit or debit card information is required to verify the authenticity of your account" ([blog](https://databento.com/blog/why-payment-information-required)) (אומת היום). מחיר Standard לא אומת (JS). **CORS:** ⚠️ אין ACAO. **בדיקה חיה:** 📄 (`https://hist.databento.com/v0/metadata.list_datasets` → 401).

**16. dxFeed — שרת demo ציבורי**
- **איחור:** ~15 דק' נמדד: שעת שרת 16:54:53 UTC, עסקה אחרונה 16:39:44 UTC. "All demo environments are available without credentials" ([KB](https://kb.dxfeed.com/en/getting-started.html)) (אומת היום).
- **היסטוריה:** נרות יומיים מ־1994-12-22 (תקרה 8,000 לבקשה); 1-min כשבועיים. **bid/ask:** אירוע `Quote` עם גודל ובורסה; `Trade`/`Summary` עם dayVolume (אומת היום). סימבולים זרים (`TEVA:TA`, `VOD:LSE`) → `TIMED_OUT`.
- **ToS:** אין ל־demo; "for testing purposes with delayed data". **מלכודת:** אין התחייבות לזמינות. **תמחור:** הצעת מחיר ([dxFeed APIs](https://dxfeed.com/dxfeed-apis/)). **CORS:** `*`. **בדיקה חיה:** ✅.
```
curl 'https://demo.dxfeed.com/webservice/rest/events.json?events=Quote,Trade&symbols=AAPL'
{"status":"OK","Quote":{"AAPL":{"bidPrice":335.68,"bidSize":40,"askPrice":335.7,"askSize":120}},
 "Trade":{"AAPL":{"time":1791391184907,"price":335.7,"dayVolume":1.3966369426749E7}}}
היסטוריה: events=Candle&symbols=AAPL{=d}&fromTime=19900101-000000
```

**17. Intrinio** — Developer Sandbox (Dow 30), ניסיון 14 יום, תוכניות מ־~‎$150/חודש ([Developer Sandbox](https://about.intrinio.com/developer-sandbox), [G2](https://www.g2.com/products/intrinio-financial-data-api/pricing)) (לא אומת). `https://api-v2.intrinio.com/securities/AAPL/prices/realtime` → 401, `access-control-allow-origin: *`. 📄.

**18. Barchart OnDemand** — [free-market-data-api](https://www.barchart.com/ondemand/free-market-data-api) החזיר תגובה ריקה. `https://ondemand.websol.barchart.com/getQuote.json?symbols=AAPL` → 401 "API key is missing or not valid." מעבר לזה לא אומת. 📄.

#### מקורות ציבוריים ללא מפתח, ארה"ב (A3)

הבהרה: כל המקורות כאן **לא רשמיים**. אין מסמכי API, אין SLA, והם יכולים להשתנות או להיחסם בלי הודעה. היום זה קרה ל־Stooq ול־`restQuote.htm` של CNBC. רובם דורשים User-Agent של דפדפן.

**19. Yahoo Finance (לא רשמי)** — מאוחד מ־A3, A4 ובדיקות העורך
- **איחור:** AAPL: `quoteSourceName` = "Nasdaq Real Time Price", `exchangeDataDelayedBy` = 0. אלה עסקאות Nasdaq בלבד, לא SIP. נמדד: `regularMarketTime` 16:52:28Z מול שעון 16:52:29Z (כשנייה, A3); 16:53:17 מול 16:53:20 (A4); **בדיקת העורך 17:02:28Z: הנר האחרון 17:02:28Z, הפרש 0 שניות**. TEVA.TA ו־SAP.DE הגיעו עם `exchangeDataDelayedBy: 15` (A3). טבלת העזרה הרשמית ([SLN2310](https://help.yahoo.com/kb/SLN2310.html), אומת היום):

  | בורסה | סיומת | עיכוב |
  |---|---|---|
  | NASDAQ | — | Real-time |
  | Tel Aviv | `.TA` | 20 min |
  | London | `.L` | 20 min |
  | XETRA | `.DE` | 15 min |
  | Euronext Paris | `.PA` | 15 min |
  | Tokyo | `.T` | 20 min |
  | Hong Kong | `.HK` | 15 min |
  | OPRA (אופציות) | — | 15 min |

  **לא הוכרע:** השדה ל־TEVA.TA אמר 15, הטבלה אומרת 20.
- **היסטוריה (אומת היום):** `range=5y&interval=1d` → 1,255 נרות (AAPL) / 1,252 (TEVA.TA). ראשית הנתונים: AAPL 1984-12, TEVA.TA 2002-08, LUMI.TA 2007-12, VOD.L 1988, SAP.DE 1998, MC.PA 2000, 7203.T 1999, 0700.HK 2004. **`range=max` מוריד את הרזולוציה**: בדיקת העורך (17:03:42Z) החזירה `dataGranularity: 3mo`, 169 נרות, גם מ־query1 וגם מ־query2. לנתונים יומיים ארוכים: `period1/period2` או `range=10y`. תוך־יומי: `1m` עד 8 ימים לבקשה ורק ב־30 הימים האחרונים ("Only 8 days worth of 1m granularity data are allowed to be fetched per request"); `5m` עד 60 יום (4,644 נרות ל־AAPL; 5,517 ל־TEVA.TA); `1h` 730 יום. TEVA.TA 1m היום: 451 נרות (06:59–14:29 UTC).
- **נפח / bid-ask:** `regularMarketVolume` 14.6M. v7 `bid/ask` ל־AAPL: 333.12 / 339.32 כשהמחיר 336.02 → **לא NBBO, לא אמין לספרד** (אומת היום).
- **עולות/יורדות:** `https://query2.finance.yahoo.com/v1/finance/screener/predefined/saved?scrIds=day_gainers&count=5&crumb=<CRUMB>` → 33 תוצאות (A3: 33, A4 עם count=5: PENG 15.12, BSP 10.43, NWE 8.75, BKH 8.68, BRZE 7.76) (PENG +15.7%, BSP +10.4%). A3: עבד גם בלי crumb; A4: עם crumb. `day_losers` עובד; `most_actives` לא נבדק. רק ארה"ב.
- **אופציות (v7):** `https://query2.finance.yahoo.com/v7/finance/options/AAPL?crumb=<CRUMB>`. בלי crumb: `401 Invalid Crumb`. crumb: cookie מ־`https://fc.yahoo.com` ואז `/v1/test/getcrumb`. שדות: strike, lastPrice, bid, ask, volume, openInterest, impliedVolatility, inTheMoney, lastTradeDate. **אין Greeks, אין היסטוריה.** 23 פקיעות עד 2029-01-19; לכל פקיעה `&date=<epoch>`. איחור נמדד ~15 דק' (עסקה אחרונה 16:38:04 בשעה 16:53) (אומת היום).
- **מגבלות:** "rate limits at Yahoo's absolute and sole discretion" ([API terms](https://legal.yahoo.com/us/en/yahoo/terms/product-atos/apiforydn/index.html)). 30 בקשות רצופות עברו. **בלי User-Agent → 429 מיד.** A4: `query1` החזיר 429 בקריאה ראשונה, `query2` עבד; A3 והעורך: `query1` 200 (**לא הוכרע**, כנראה לפי IP).
- **CORS:** ❌ אין ACAO (אומת היום, A3+A4). **ToS** ([Yahoo ToS](https://legal.yahoo.com/us/en/yahoo/terms/otos/index.html)): אסור "access or collect data … using any automated means … robots, spiders, scrapers … for any purpose without our express, prior permission" ו־"may not access or reuse the Services … for any commercial purpose" (אומת היום). README של [yfinance](https://github.com/ranaroussi/yfinance): "intended for personal use only".
- **מלכודות:** `ILA` = אגורות, `GBp` = פני → לחלק ב־100. מחיר TEVA.TA ב־Yahoo (11990) זהה לסגירה באתר TASE (אומת היום).
- **בדיקה חיה:** ✅.
```
curl -A "$UA" 'https://query1.finance.yahoo.com/v8/finance/chart/AAPL?interval=1m&range=1d'
→ meta.regularMarketTime=1791391948 (16:52:28Z) price 336.12  (A3)
→ העורך 17:02:28Z: HTTP 200, price 336.0, dataGranularity 1m, 214 נרות, נר אחרון 17:02:28Z
query2: curl -s -A 'Mozilla/5.0 ...' 'https://query2.finance.yahoo.com/v8/finance/chart/<SYM>?interval=1d&range=max'
  TEVA.TA 200 Tel Aviv ILA price 11990.0 rmt 2026-10-07 14:28 UTC first 2002-08-31  (A4)
```

**20. Stooq** — ❌ נכשל (A3 16:54, A4 16:54:48, העורך 17:02:30Z)
- `https://stooq.com/q/l/?s=aapl.us&f=sd2t2ohlcv&h&e=csv` → **HTTP 404** "The page you requested does not exist or has been moved", לכל הסיומות (`.us .uk .de .jp .hk .ta .fr`). גם stooq.pl.
- `https://stooq.com/q/d/l/?s=aapl.us&i=d` → HTTP 200 אבל דף "This site requires JavaScript to verify your browser" (proof-of-work SHA-256 ב־JS), בלי CSV. גם דף הבית ו־`/db/h/`.
- איחור, מגבלה יומית, כיסוי, ToS: לא נבדקו. **מסקנה: לא להסתמך על Stooq.**

**21. Nasdaq.com (`api.nasdaq.com`, לא רשמי)**
- **איחור:** `isRealTime: true`, Nasdaq Last Sale. `lastTradeTimestamp` "12:52 PM ET" מול 16:52:30Z → פחות מדקה, רזולוציה דקות (אומת היום).
- **היסטוריה:** `/historical?…&limit=9999` → 2,513 שורות = 10 שנים OHLCV; `/chart` → נרות דקה של היום מ־4:00 ET (534 נקודות) (אומת היום).
- **bid/ask:** `bidPrice $336.11`, `askPrice $336.16`, `bidSize 25`, `askSize 40`, `volume 14,590,589`. ספרד 5 סנט, נראה אמיתי (אומת היום).
- **עולות/יורדות:** `/api/marketmovers?assetclass=stocks` → `MostAdvanced`, `MostDeclined`, `MostActiveByShareVolume`, `MostActiveByDollarVolume`, `Nasdaq100Movers`. היום: SBFM +158.9%, DYNCW +133%, SXTC +128%. כולל warrants ומניות זולות (אומת היום).
- **מגבלות:** 30 רצופות עברו. **בלי UA אין תגובה כלל** (timeout). **CORS:** ❌.
- **ToS** ([nasdaq.com/legal](https://www.nasdaq.com/legal)): "personal, limited, revocable … license … solely for your personal, non-commercial use. … you agree not to sell, copy, distribute, or create derivative works"; איסור scraping ל־"training or development of artificial intelligence systems" (אומת היום).
- **בדיקה חיה:** ✅.
```
curl -A "$UA" -H 'Accept: application/json' 'https://api.nasdaq.com/api/quote/AAPL/info?assetclass=stocks'
→ "lastSalePrice":"$336.13","lastTradeTimestamp":"Oct 7, 2026 12:52 PM ET","isRealTime":true,
   "bidPrice":"$336.11","askPrice":"$336.16","bidSize":"25","askSize":"40","volume":"14,590,589"
```

**22. Cboe delayed quotes JSON (CDN ציבורי)** — A3 + A4
- **איחור:** `last_trade_time` 12:36:56 ET מול 12:52:39 ET → ~15.7 דק' (A3); 12:38:06 מול 12:53:39 → 15 דק' (A4). `timestamp` בקובץ הוא זמן יצירה ולא זמן המחיר (אומת היום).
- **מניה:** `bid 335.35`, `ask 335.39`, `bid_size 120`, `ask_size 80`, OHLC, `prev_day_close`, `volume`, `iv30`.
- **אופציות:** `…/delayed_quotes/options/AAPL.json` → **3,568 חוזים**, 1.59MB; לכל חוזה bid/ask/size, iv, open_interest, volume, delta/gamma/vega/theta/rho, theo, last_trade_time. `_SPX` = 13.4MB. **המקור החינמי היחיד עם שרשרת מלאה + Greeks** (אומת היום).
- **טכני:** `https://cdn.cboe.com/api/global/delayed_quotes/options/AAPL.json` → 307 ל־`https://cdn-api.cboe.com/...`, צריך `curl -L`. `cache-control: s-maxage=5`.
- **היסטוריה / עולות־יורדות:** אין. **CORS:** ❌. **ToS** ([cboe.com/terms](https://www.cboe.com/terms)): "view, print and download one copy of the Materials for your personal non-commercial use … may not otherwise copy, reproduce, alter, store … display, broadcast, create a derivative work" (אומת היום). שמירה במסד נתונים = תחום אפור.
- **בדיקה חיה:** ✅.
```
curl -sL -A "$UA" 'https://cdn.cboe.com/api/global/delayed_quotes/quotes/AAPL.json'
→ {"timestamp":"2026-10-07 16:52:05","data":{"current_price":335.365,"bid":335.35,"ask":335.39,"bid_size":120,"ask_size":80,
   "volume":13081621,"last_trade_time":"2026-10-07T12:36:56"}}
curl -sL 'https://cdn.cboe.com/api/global/delayed_quotes/options/AAPL.json' → 200, 1,588,606 bytes
   {"option":"AAPL270115C00250000","bid":88.6,"ask":90.15,"iv":0.3485,"open_interest":24488.0,"delta":0.9674,...}
```

**23. TradingView scanner (לא רשמי)**
- **איחור:** `update_mode` = `"delayed_streaming_900"` → 15 דק' לאנונימי. `close 335.438` תואם ל־Cboe המושהה (אומת היום). **bid/ask:** `null`. **נפח:** 13,891,951.
- **עולות/יורדות:** מיון לפי `change`; בלי סינון מחזיר OTC זניחות (MEGH +14,999,900%). `totalCount` 11,851. **היסטוריה:** אין. **שווקים אחרים:** `/israel/scan`, `/germany/scan` לא נבדקו.
- **CORS:** ✅ משקף Origin; preflight מתיר רק `Referer,Accept` → בדפדפן לשלוח body בלי `Content-Type: application/json` (לא נבדק בדפדפן אמיתי).
- **ToS** ([policies](https://www.tradingview.com/policies/)): "licensed for exclusive display-only use", "we do not permit commercial usage of any of our services or APIs", "strictly forbid the sublicensing … or any distribution of TradingView content, including market data" (אומת היום). שימוש מתוכנת = כנראה non-display use אסור.
- **בדיקה חיה:** ✅.
```
curl -X POST 'https://scanner.tradingview.com/america/scan' -H 'Content-Type: application/json' \
 -d '{"symbols":{"tickers":["NASDAQ:AAPL"]},"columns":["close","volume","change","bid","ask","update_mode"]}'
→ {"data":[{"s":"NASDAQ:AAPL","d":[335.438,13891951,0.5419,null,null,"delayed_streaming_900"]}]}
```

**24. Finviz**
- **איחור:** "Stock quotes delayed by 1 minute. Futures and options delayed by 15 minutes." ([quote.ashx?t=AAPL](https://finviz.com/quote.ashx?t=AAPL)) (אומת היום). נמדד 336.00 ב־16:55 מול Nasdaq 335.84 ב־12:54 ET.
- **עולות/יורדות:** `screener.ashx?v=111&s=ta_topgainers` (HTML): SXTC, SBFM, PFAI, TOPP, BIYA (אומת היום). **export CSV:** דורש Elite. **היסטוריה/bid-ask/אופציות:** אין בחינם.
- **פוסלים:** Elite דורש כרטיס אשראי גם לניסיון (אומת היום). **CORS:** ❌. **ToS:** `/terms` → 404; מקור משני [dev.co](https://dev.co/databases/open-source/finviz) (לא אומת).
- **תוכנית:** Elite ‎$39.50/חודש או ‎$299.50/שנה, זמן אמת + export ([finviz.com/elite](https://finviz.com/elite)) (אומת היום). **בדיקה חיה:** ✅ scrape.
```
curl -sL -A "$UA" 'https://finviz.com/quote.ashx?t=AAPL' → class="quote-price_price">336.00 … "Stock quotes delayed by 1 minute."
```

**25. Google Finance**
- **איחור:** `$335.89` ב־16:55 תואם למחיר החי; חותמת זמן בדף "12:49:46 PM GMT-4" פיגרה ~5.5 דק' (אולי cache) (אומת היום). [Disclaimer](https://www.google.com/googlefinance/disclaimer/): NASDAQ/NYSE "Realtime", TASE (TLV) 20 דק' (אומת היום).
- **אין API**, scrape שביר (class מוצפן, `YMlKec`). **CORS:** ❌.
- **ToS:** "You agree not to copy, modify, reformat, download, store, reproduce, reprocess, transmit or redistribute any data" ([disclaimer](https://www.google.com/googlefinance/disclaimer/), [policies.google.com/terms](https://policies.google.com/terms)) (אומת היום). **בדיקה חיה:** ✅ scrape.
```
curl -A "$UA" 'https://www.google.com/finance/quote/AAPL:NASDAQ' → 200, 1.36MB; <span>$335.89</span>
```

**26. CNBC quote webservice (לא רשמי)**
- `restQuote.htm?symbols=AAPL&output=json` → ❌ **404**. החלופה `quote.htm?…&requestMethod=itv&output=json` ✅: `last 335.97`, `last_timedate "12:55 PM EDT"`, `realTime "true"`, `source "Last NASDAQ LS, VOL From CTA"`; פחות מדקה. אין bid/ask (אומת היום).
- **CORS:** ✅ `access-control-allow-origin: *` → המקור היחיד בזמן אמת שנקרא ישירות מדפדפן. **ToS:** 403 (לא אומת).
```
curl -A "$UA" 'https://quote.cnbc.com/quote-html-webservice/quote.htm?symbols=AAPL&requestMethod=itv&noform=1&partnerId=2&fund=1&exthrs=1&output=json'
→ {"symbol":"AAPL","last":"335.97","last_timedate":"12:55 PM EDT","realTime":"true","volume":"14,090,183"}
```

#### תל אביב, אירופה, אסיה ואופציות (A4)

**27. TASE DataHub — ה־API הרשמי של הבורסה**
- פורטל [datahubapi.tase.co.il](https://datahubapi.tase.co.il/) על Kong Konnect. `GET https://datahubapi.tase.co.il/api/v2/portal` → `"is_public":false,"oidc_auth_enabled":true`; `https://datahubapi.tase.co.il/api/v2/products` → 401 (אומת היום). אפילו הקטלוג סגור בלי חשבון.
- **מוצרים חינמיים:** לפי [tasepy](https://pypi.org/project/tasepy/): "4 free API endpoints: Funds, Indices Basic, Indices Online, Securities Basic"; מפתח ב־[tase.co.il … datahub](https://www.tase.co.il/en/content/products_lobby/datahub) (לא אומת מול TASE; הדף מפנה ל־`data_services`, SPA לא קריא).
- איחור, מכסות, ToS, מחיר: לא אומת. עיתונות 2020: [israelhayom](https://israelhayom.com/2020/09/25/tel-aviv-stock-exchange-offering-streamlined-access), [mondovisione](https://mondovisione.com/media-and-resources/news/tase-data-hub-the-tel-aviv-stock-exchange-is-launching-a-data-system-that-allo-2020101/). **בדיקה חיה:** 📄. **המלצה:** שהמשתמש ייכנס לפורטל בעצמו ויבדוק את Securities Basic.

**28. אתר הבורסה — `api.tase.co.il` (לא רשמי)**
- **איחור:** נבדק אחרי הסגירה: `"LastDealTime":"EoD"`, `"TradeDate":"07/10/2026"`. תוך־יומי לא נבדק (לא אומת).
- **היסטוריה:** `POST /api/security/historyeod` → OHLC יומי + מחזור ביחידות ובשקלים (אומת היום). לפי TASE מ־2000 ([mondovisione](https://mondovisione.com/news/tase-launches-new-website-for-the-first-time-all-trading-data-from-the-year-2000-2011124/)) (לא אומת ב־API).
- **bid/ask:** לא נמצאו. **מגבלות:** Incapsula; בלי `Referer: https://market.tase.co.il/` → דף חוסם. **CORS:** ❌ 403 עם Origin זר. **ToS:** לא נמצא (SPA); להניח API פנימי בלי רשות.
- **מחירים באגורות:** 11990 = 119.90 ₪. **בדיקה חיה:** ✅ (16:53:59 UTC).
```
curl -s -A 'Mozilla/5.0 ...' -H 'Referer: https://market.tase.co.il/' 'https://api.tase.co.il/api/company/securitydata?securityId=629014&lang=1'
{"Symbol":"TEVA","LastRate":11990.0,"Change":-0.17,"TradeDate":"07/10/2026","LastDealTime":"EoD","OverallTurnOverUnits":1153909,"TurnOverValueShekel":137590220.00}
curl -s -X POST -H 'Referer: https://market.tase.co.il/' -H 'Content-Type: application/json' https://api.tase.co.il/api/security/historyeod \
  -d '{"dFrom":"2021-10-07","dTo":"2026-10-07","oId":"00629014","pageNum":1,"pType":"8","TotalRec":1,"lang":"1"}'
{"Items":[{"TradeDate":"07/10/2026","OpenRate":11900.0,"CloseRate":11990.0,"HighRate":12090.0,"LowRate":11820.0,...
פועלים (securityId=662577): POALIM 7400.0 07/10/2026 EoD 4160313
```

**29. Maya (`https://mayaapi.tase.co.il/api/company/details?companyId=629&lang=1`)** — ❌ "This page can't be displayed due to a security violation" (16:53 UTC). דיווחי חברות, לא מחירים; לא רלוונטי.

**30. Investing.com** — ❌ 403 (Cloudflare) על [דף TEVA](https://www.investing.com/equities/teva-pharmaceutical-industries-ltd). אין API; ToS אוסרים scraping (לא אומת). לא מומלץ.

**31. Börse Frankfurt (`api.boerse-frankfurt.de`, לא רשמי)**
- `"timestampLastPrice":"2026-10-07T18:39:14+02:00"` (16:39 UTC) בשעה 16:55, `tradingTimeEnd 22:00` → כנראה מחיר פרנקפורט ולא סגירת Xetra; איחור לא אומת. **אי־התאמה:** Yahoo SAP.DE = 187.62 = `closingPricePrevTradingDay` כאן, בעוד `lastPrice` 188.02 (**לא הוכרע**).
- בלי מפתח, עבד גם בלי Referer. **CORS:** ❌ 403 עם Origin זר. ToS לא נבדק. **בדיקה חיה:** ✅.
```
curl -s 'https://api.boerse-frankfurt.de/v1/data/price_information/single?isin=DE0007164600&mic=XETR'
{"closingPricePrevTradingDay":187.62,"lastPrice":188.02,"mic":"XETR","timestampLastPrice":"2026-10-07T18:39:14+02:00","turnoverInPieces":1671276}
```

**32. Euronext live / JPX** — Euronext `https://live.euronext.com/en/ajax/getDetailedQuote/FR0000121014-XPAR` מחזיר JSON מוצפן (`{"ct":"nDoEKry..."}`); `api.live.euronext.com` לא ענה ❌. JPX לא נבדק. לא נמצא feed חינמי פתוח באירופה/אסיה מלבד Börse Frankfurt.

**33. Cboe All Access API (בתשלום)** — [datashop.cboe.com/cboe-all-access-api](https://datashop.cboe.com/cboe-all-access-api): Free Trial 500 points/day ל־14 יום, "Credit card authorization is required" → **נפסל כחינמי** (אומת היום). Tier 1 ‎$599/חודש ([pricing](https://datashopcert.livevol.com/all-access-apis-pricing), מתוצאת חיפוש, לא אומת). 📄.

**34. optionsDX** — [optionsdx.com/shop](https://www.optionsdx.com/shop/): 10 מוצרים ב־$0.00 (SPY, SPX, VIX, BTC, QQQ, TSLA, AAPL, UVXY, SLV, NVDA Option Chains) (אומת היום). שנים, תדירות וצורך בחשבון לא פורטו. הורדת קבצים, לא API. 📄.

**35. Tradier sandbox (אופציות)** — ראו #10. `GET https://sandbox.tradier.com/v1/markets/options/chains?symbol=AAPL&expiration=2026-10-09` → 401 (אומת היום).

## חלק ב — מה אפשר ללמוד מחשבונות דמו קיימים

הערת גישה (B1): דפים רשמיים של interactivebrokers.com / ibkr.info, paperMoney ב־schwab.com, investopedia.com/simulator ו־marketwatch.com/games חסמו גישה אוטומטית (403/401). טענות עליהם מסומנות (לא אומת). "מתועד" = מה שהפלטפורמה כותבת; "משתמשים מדווחים" = פורומים, Reddit, חנויות אפליקציות. טענות "מזיכרון" של העובד מסומנות **(לא אומת, ללא קישור)**.

### טבלת פלטפורמות

| פלטפורמה | מקור מחירים/איחור | מודל מילוי (ספרד/החלקה/מילוי חלקי) | סטופ בפער | שורט | מינוף/סגירה כפויה | אופציות | עמלות | הבדל מהאמיתי |
|---|---|---|---|---|---|---|---|---|
| **Interactive Brokers Paper** | נתונים אמיתיים לפי מנויי החשבון האמיתי; בלי שיתוף: ארה"ב 15 דק', עתידים 10 דק' | מילוי מ־top of book, בלי עומק; החלקה ומילוי חלקי לא מתועדים | לא מתועד; "סטופים תמיד מדומים" | לא נמצא (מזיכרון: כמו האמיתי, לא אומת) | $1M התחלתי; מרג'ין כמו האמיתי; חיסול לא נמצא | יש; אין penny-trading; מימוש/הקצאה לא נמצאו | לא נמצא | חסרים VWAP/Auction; תלונות על מילויים טובים מהלימיט |
| **Schwab thinkorswim paperMoney** | אמיתי; 15 דק' עד חתימה על הסכמים | "מחיר מילוי תיאורטי" לפי עסקאות אמיתיות; צד־שלישי: מניה ב־last, אופציות ב־mid, גודל לא משפיע | לא נמצא; צד־שלישי: mark בפתיחה | לא נמצא רשמית | סוג מרג'ין לבחירה; "לא משקף את כל מאפייני המרג'ין התוך־יומי" | יש | "paper commissions" (לא אומת) | אין מחוץ לשעות, אין MOO/MOC; "instant fills" |
| **TradingView Paper** | ציטוטי TV; חינם: ארה"ב 15 דק' (לא אומת) | **קנייה ב־Ask, מכירה ב־Bid**, גרף ב־mid | לא נמצא | כל סימבול, בלי בדיקת השאלה ובלי עמלה | מינוף לפי נכס; דחייה בלי מרג'ין; margin call לא מתועד | לא נמצא | **כבוי כברירת מחדל** | לימיט/סטופ "מופעלים במחירים שלא הגיעו" |
| **Webull Paper** | לא מתועד; צד־שלישי: זמן אמת | מרקט "מיד במחיר השוק", רק בשעות המסחר; "אם אין מחזור, לא יתמלא" | לא נמצא | לא נמצא | לא נמצא | עד Level 4 | לא נמצא | נמחק אחרי 180 יום |
| **eToro Virtual** | "אותם תנאי שוק"; איחור לא נמצא | לא נמצא | אמיתי: SL "not guaranteed" (לא אומת) | CFD; בדמו בלי עמלות | מינוף/SL/TP (לא אומת) | לא נמצא | **"does not incur any fees"** | רווח מנופח; $100K; איפוס רק דרך תמיכה (לא אומת) |
| **Trading 212 Practice** | לא נמצא | לא נמצא לדמו | אמיתי: FOS $31.49→$60.01 (לא אומת) | רק CFD (לא אומת) | CFD ממונף; stop-out לא נמצא | אין (לא אומת) | לא נמצא | רק איפוס, אין הוספת כסף; ממשק מבלבל |
| **Investopedia Simulator** | ~15 דק' (צד־שלישי) | במחיר המושהה, בלי החלקה; **אין מגבלת נזילות** | לא נמצא | לפי יוצר המשחק; מינימום ~$5 (לא אומת) | לפי יוצר המשחק | קנייה בלבד | יוצר המשחק | לוח מובילים "נפרץ" דרך מניות פני |
| **MarketWatch VSE** | ~15–20 דק' (לא אומת) | מחיר מושהה; הגבלת נפח אפשרית | לא נמצא | toggle | toggle + ריבית ניתנת להגדרה (לא אומת) | כנראה אין | לפי עסקה | האפליקציה הוסרה; מצב האתר לא אומת |
| **NinjaTrader Sim101** | פיד חי או Market Replay (לא אומת) | **מנוע מילוי מתקדם לפי מחזור**; "Enforce immediate fills" / "Enforce partial fills"; בבקטסט החלקה בטיקים | החלקה מוגבלת לטווח הנר הבא | לא רלוונטי | תבניות סיכון (לא אומת) | לא נמצא | תבניות (לא אומת) | לימיט "מתמלא בקלות מדי בנגיעה" |
| **Alpaca Paper** | IEX בלבד, זמן אמת | מול **NBBO**; **מילוי חלקי אקראי ב־10%**; **בלי בדיקת כמות מול נזילות** | לא מתועד; מילויים מחוץ לנר | יש; עמלת השאלה "Coming Soon" | מרג'ין יש; PDT (לא אומת); חיסול לא מתועד | יש; מימוש אוטומטי ITM ≥$0.01 | **אין** | "הסביבות לא תמיד זהות" |
| **TradeStation SIM** | רק סימבולים עם הרשאת זמן אמת | **"instant fills"** | לא נמצא | לא נמצא | לא נמצא | לא נמצא | לא נמצא | "SIM לא טוב להערכת P/L" (לא אומת) |
| **MetaTrader 5 Demo** | שרת הדמו של הברוקר | Ask/Bid; 4 מצבי ביצוע; FOK/IOC | לא מתועד (מזיכרון, לא אומת) | Sell CFD + swap (לא אומת) | נוסחת מרג'ין מתועדת; stop-out לפי ברוקר | לא נמצא | לפי ברוקר | "אי אפשר לצפות להרוויח" |
| **Stock Trainer / Wall Street Survivor / HowTheMarketWorks** | ST "זמן אמת"; WSS זמן אמת בארה"ב, 15–20 בחו"ל; HTMW "לפחות 15 דק'" | **WSS: Bid/Ask בזמן אמת**; HTMW "קרוב ל־Ask/Bid" (לא אומת) | ST: סטופ "הופעל" בלי שהמחיר הגיע | WSS לפי תחרות | WSS: מרג'ין התחלתי + מחיר מינימלי | WSS קנייה בלבד | לפי מנהל | WSS: תקרות ריכוז; ST: באגים, התקדמות לא נשמרת |

### תשובות לפי פלטפורמה

**1. Interactive Brokers — Paper Trading**
1. **מקור/איחור:** חשבון הנייר משקף את מנויי החשבון האמיתי (אומת היום, [aboutpapertradingaccounts](https://ibkrguides.com/clientportal/aboutpapertradingaccounts.htm)). טבלת איחור: [ibkb 1719](https://ibkb.interactivebrokers.com/article/1719) (לא אומת, 403).
2. **מילוי:** "Fills are simulated from the top of the book; no deep book access" (אומת היום). מילויים "may or may not be within the bounds of the live market" ([ibkb 252](https://ibkb.interactivebrokers.com/article/252), לא אומת). החלקה/מילוי חלקי: לא מתועדים.
3. **סטופ בפער:** "Stops and other complex order types are always simulated in paper trading, which may result in slightly different behavior" (אומת היום). כלל פער: לא נמצא.
4. **שורט:** לא נמצא מסמך (מזיכרון, לא אומת, ללא קישור).
5. **מינוף:** "USD 1,000,000 of paper trading Equity with Loan Value" (אומת היום). חיסול: לא נמצא.
6. **אופציות:** יש; penny-trading לא נתמך, קומבינציות מוגבלות (אומת היום). מימוש/הקצאה: לא נמצא.
7. **עמלות:** לא נמצא.
8. **הבדל מהאמיתי:** "you may encounter some differences due to its construction as a simulator"; חסרים VWAP, Auction, RFQ, Pegged to Market; איפוס "up to five times your production account's value" (אומת היום). משתמשים: "Limit sell 2418 shares at 7.97. Sold at 8.13. Should be so easy to fix." ([elitetrader](https://elitetrader.com/et/threads/paper-trader-seriously-flawed.386365/), אומת היום); "for FX, it's stale quotes. For options, it's incomplete prices." ([elitetrader latest](https://elitetrader.com/et/threads/paper-trader-seriously-flawed.386365/latest)); "whenever there are spikes in price and volatility my mkt orders don't even fill right away" ([reddit](https://reddit.com/r/algotrading/comments/1rrxemv/how_are_you_testing_your_systems_live/), דרך mirror).
9. **מסכים:** TWS/Client Portal/מובייל: רשימות מעקב, גרפים, כל הפקודות, תיק ורווח/הפסד, דוחות; איפוס כסף ב־Client Portal (אומת היום). אין לוח מובילים.

**2. Schwab thinkorswim — paperMoney**
1. 15 דק' עד חתימה על הסכמי pro/non-pro ([FAQ](https://toslc.thinkorswim.com/center/faq/Most-Common-Questions), אומת היום). טענה ישנה על 20 דק': לא אומת.
2. "a theoretical fill price is assigned… based on the actual fill prices the underlying security traded at during the time interval" ([schwab.com](https://www.schwab.com/content/thinkorswim-papermoney-stock-trading-simulator), לא אומת, 403). צד־שלישי: "generally filled at the LAST traded price", אופציות ב־mark, "Trade size is not a factor" ([investingdaily](https://investingdaily.com/paper-trading), אומת היום). → אין החלקה לפי גודל, אין מילוי חלקי.
3. צד־שלישי: "As soon as the market opens, you may get filled at the spread's 'mark'" (אומת היום, אותו מקור).
4. בלוג באמינות נמוכה: "Short selling always available" ([traderspost](https://blog.traderspost.io/article/does-schwab-have-paper-trading), אומת היום).
5. "Account Margin Type" לבחירה ([FAQ Accounts](https://toslc.thinkorswim.com/center/faq/Accounts)); "paperMoney may not reflect all intraday margin features" (אומת היום). חיסול: לא נמצא.
6. יש (גם עתידים ופורקס). מימוש/הקצאה: לא נמצא.
7. "paper commissions" ([workplace.schwab.com](https://workplace.schwab.com/story/practice-trading-risk-free-with-papermoney), לא אומת).
8. אין מחוץ לשעות, אין MOO/MOC (לא אומת). $100K, חשבון "D-" (אומת היום). משתמשים: "I've been getting phantom market orders with no stoploss placed on Paper Money." ([usethinkscript](https://usethinkscript.com/threads/phantom-paper-money-trades.21766/latest), אומת היום); "absurdly simple and unrealistic, with instant fills all the time no matter what order type" ([reddit](https://www.reddit.com/r/thinkorswim/comments/1leoipz/futures_paper_money_problems), לא אומת); מנוע "very bad", אפשר "buy routinely at the bid and sell routinely at the ask" ([futures.io](https://futures.io/thinkorswim/38747-paper-vs-real.html), לא אומת).
9. ערכה כתומה = נייר (לא אומת); Monitor (Activity and Positions), Analyze, גרפים; "Adjust Account" / "Reset All Balances and Positions" (אומת היום). אין לוח מובילים.

**3. TradingView — Paper Trading**
1. ההסתייגות על איחור חלה "Once you connect to a real broker (not Paper Trading)" ([43000739323](https://www.tradingview.com/support/solutions/43000739323/), אומת היום). חינם: 15 דק' ([coincub](https://coincub.com/blog/tradingview-paper-trading/), לא אומת).
2. "Orders are executed at the Ask and Bid prices… while the chart data is built at the middle price" ([43000647247](https://www.tradingview.com/support/solutions/43000647247), אומת היום). מתחרה: מילוי 100% בנגיעה ראשונה ([tapeboard](https://tapeboard.com/compare/tradingview-paper-trading-alternative), לא אומת).
3. לא נמצא. הסקת העובד (לא מתועד): סטופ → מרקט בציטוט הראשון.
4. שורט לכל סימבול, סגירה ב־Ask (אומת היום). השאלה/עמלה: לא נמצאו.
5. "if the required margin… exceeds your available margin, the order will not be executed"; "TradingView does not provide real margin trading… We only simulate the mechanics" ([43000742700](https://www.tradingview.com/support/solutions/43000742700-how-to-trade-with-leverage-in-paper-trading/), אומת היום). ברירות מחדל 1:1/20:1/50:1 (לא אומת). margin call: לא נמצא.
6. לא נמצא.
7. "The commission is inactive by default" ([43000755797](https://www.tradingview.com/support/solutions/43000755797-what-is-commission-per-contract-in-paper-trading/), אומת היום).
8. משתמשים: לימיט וסטופ "at prices that never actually reached those levels" ([reddit](https://www.reddit.com/r/TradingView/comments/1kpcvl2/paper_trading_is_buggy), לא אומת); "15–25% better than live" (tapeboard, לא אומת).
9. "Order ticket, Depth of market, and Chart trading" ([43000516466](https://www.tradingview.com/support/solutions/43000516466-paper-trading-main-functionality/), אומת היום); Account Manager; איפוס (לא אומת).

**4. Webull — Paper Trading** (מקור: [FAQ 11069](https://www.webull.com/help/faq/11069-Paper-Trading), אומת היום)
1. לא מתועד; צד־שלישי זמן אמת ([benzinga](https://www.benzinga.com/money/how-to-paper-trade-on-webull), לא אומת).
2. "Market orders are only executed during regular trading hours… and fill immediately at the current market price"; "If there is no trading volume after you place an order, it will not be filled". Bid/Ask, החלקה, מילוי חלקי: לא נמצא. הודעה 2025 על "pricing engine designed to better reflect live market conditions" (לא אומת).
3. רק "when the specified price or condition is met".
4. לא נמצא. 5. לא נמצא; Futu: [US Margin Paper Trading Rules](https://support.futunn.com/en/topic814) (לא אומת).
6. "all options strategies available for live trading, including Level 3 and Level 4" (אומת היום).
7. לא נמצא.
8. "all transactions and funds are not real"; נמחק אחרי 180 יום (אומת היום). ציטוט מאומת: לא נמצא ([r/Daytrading](https://www.reddit.com/r/Daytrading/comments/1cte14f/paper_trading_on_webull_help/), לא נקרא).
9. כפתורים כתומים; מסחר 24 שעות שני–שישי בנבחרות (אומת היום); Reset ל־$100K (לא אומת).

**5. eToro — Virtual Portfolio**
1. "replicates the same features and market conditions as a real investing account", "real-time trends" ([demo-account](https://www.etoro.com/en-us/trading/demo-account/), אומת היום). איחור: לא נמצא.
2. לא נמצא. 3. אמיתי: SL "not guaranteed", "at the next available rate" ([BE Policy PDF](https://etoro.com/wp-content/uploads/2018/12/eToroEU_BE_Policy_v11.18.pdf), לא אומת).
4. CFD; הדמו "does not incur any fees" (אומת היום). 5. מינוף/SL/TP (לא אומת). 6. לא נמצא. 7. "does not incur any fees nor affect your actual account balance" (אומת היום).
8. רווח מנופח; $100K; איפוס דרך תמיכה (לא אומת). תלונה כללית: "ETORO set prices to ensure you lose your money" ([forexpeacearmy](https://www.forexpeacearmy.com/community/goto/post?id=420076), לא אומת).
9. מתג Real/Virtual, CopyTrader, Smart Portfolios (אומת היום).

**6. Trading 212 — Practice Mode**
1. "zero delay" ([wikibit](https://blog.wikibit.com/?p=1103), לא אומת). 2. אמיתי: "may be executed at a different price than your target if the market opens with a gap" (לא אומת).
3. אמיתי: FOS, סטופ ב־$31.49 מולא ב־$60.01 ([DRN-5754968](https://www.financial-ombudsman.org.uk/decision/DRN-5754968.pdf), לא אומת).
4. CFD (לא אומת). 5. stop-out 50% (מזיכרון, לא אומת, ללא קישור). 6. אין (לא אומת). 7. לא נמצא.
8. (אומתו היום) "The option to 'Manage funds' is only present when the account uses real money" ([64356](https://community.trading212.com/t/add-funds-to-my-practice-account/64356)); "it looks the same at the real money version and you could get the 2 mixed up very easily" ([26095](https://community.trading212.com/t/practice-mode-suggestion/26095)); "from mobile browser I can see 'Invest' among products, but from desktop only CFD" ([1907](https://community.trading212.com/t/trading-212-invest-practice-on-desktop/1907)).
9. איפוס עד ~50K, Pies (לא אומת).

**7. Investopedia Stock Simulator**
1. "stock prices aren't updated in real time but every 15 minutes" ([stockmarketgame.net](https://stockmarketgame.net/investopedia-simulator-the-ultimate-review), אומת היום, צד־שלישי). [FAQ רשמי](https://www.investopedia.com/simulator/faq) חסום.
2. בלוג רשמי 2008: מרקט מחוץ לשעות "will fill at the stock's opening price the next day" ([wordpress](https://investopediasimulator.wordpress.com/category/bug-issuefix/), אומת היום, ישן). "no realism to the liquidity of penny stocks" (לא אומת).
3. לא נמצא. 4. יוצר המשחק; ~$5 מינימום (לא אומת). 5. יוצר המשחק. 6. "writing options is not supported" (צד־שלישי, אומת היום). 7. דוגמה 2007: "Market: $19.99, Limit: $29.95… per Contract: $1.75" ([bogleheads](https://www.bogleheads.org/forum/viewtopic.php?t=8782), לא אומת).
8. "there is just no freaking way" $100K→$500B בלי פרצה ([trade2win](https://www.trade2win.com/threads/investopedia-simulator-billionaires.192818/), לא אומת); "outdated platform, static charts ... no watchlist", "mobile experience … rather poor" (stockmarketgame.net, אומת היום).
9. לוח מובילים בכניסה, משחקים עם חוקים (אומת היום, צד־שלישי).

**8. MarketWatch Virtual Stock Exchange**
- **מצב 2026:** [Google Play](https://play.google.com/store/apps/details?id=com.dowjones.marketwatch.vse) → 404 (אומת היום); AppBrain: הוסרה 27.6.2025 (לא אומת). [Screenwise](https://screenwiseapp.com/media/marketwatch-virtual-stock-exchange-website) מתייחס לאתר כפעיל (אומת היום). האתר חוסם (401) → **לא אומת, בדיקה ידנית**.
1. ~15–20 דק' (לא אומת); "the price they see might not be the price they get" (Screenwise). 2. מושהה + הגבלת נפח (לא אומת). 3. לא נמצא. 4–5. toggles; ריבית 4%/6% (לא אומת). 6. כנראה אין. 7. לפי עסקה (לא אומת). 8. "cluttered, ad-heavy, and looks like a relic of the early 2000s" (Screenwise, אומת היום). 9. יצירת משחק עם הון, מרג'ין, שורט, עמלות, ריבית.

**9. NinjaTrader — Sim101**
1. פיד חי או Market Replay (לא אומת).
2. [options_trading.htm](https://static.ninjatrader.com/support/helpGuides/nt8/options_trading.htm) (אומת היום): "Enforce immediate fills": "filled immediately instead of using the NinjaTrader advanced simulation fill engine"; "Enforce partial fills". פורום: המנוע "should take the volume traded into consideration" ([56455](https://forum.ninjatrader.com/forum/ninjatrader-7/platform-technical-support/56455-sim-algo-for-size), לא אומת). בקטסט ([understanding_historical_fill](https://static.ninjatrader.com/support/helpGuides/nt8/understanding_historical_fill_.htm), אומת היום): "break each historical bar into three virtual bars"; החלקה "expressed in 'ticks'… only applied to market, stop-market and Market-if-touched orders".
3. החלקה מוגבלת לטווח הנר (אומת היום). 4. לא רלוונטי. 5–7. תבניות (לא אומת).
8. לימיט "getting filled too easily on touch" ([78324](https://forum.ninjatrader.com/forum/ninjatrader-7/platform-technical-support/78324-market-replay-fill-logic-bug?p=685697)); "it would not be possible for the bid price level to move ... without every order in the queue at the higher level getting filled first" ([62662](https://forum.ninjatrader.com/forum/ninjatrader-7/strategy-development-aa/62662-ninjatrader-realistic-fills/page2)) (לא אומת).
9. Control Center, SuperDOM, Chart Trader, Market Analyzer, Playback (לא אומת); רקע צבעוני (אומת היום).

**10. Alpaca — Paper Trading** (מקור: [paper-trading](https://docs.alpaca.markets/docs/paper-trading), אומת היום)
1. "you are only entitled to receive and make use of IEX market data".
2. "matched against the best available current market price (NBBO)"; "Partial fills for a random size 10% of the time"; "Your order quantity is not checked against the NBBO quantities… you can … receive a fill for an order that is much larger than the actual available liquidity". לא מדומים: market impact, latency slippage, queue position, price improvement.
3. לא מתועד. משתמש: "CCRN was never below $18", מולא ב־$17.52 ([forum 6602](https://forum.alpaca.markets/t/paper-trading-impossible-price/6602), אומת היום).
4. עמלת השאלה "Coming Soon"; אמיתי: ETB חינם, HTB עם locate ועמלה יומית ([margin-and-short-selling](https://docs.alpaca.markets/docs/margin-and-short-selling), אומת היום).
5. אמיתי: 2x לילה, 4x תוך־יומי, margin call בבוקר, חיסול בירידה חדה (אומת היום). PDT בנייר (לא אומת).
6. [options-trading](https://docs.alpaca.markets/docs/options-trading) (אומת היום): מימוש אוטומטי "ITM by at least $0.01"; "will sell-out the position within 1 hour before expiry"; רשומות "synced at the start of the following day".
7. אין עמלות, אין רגולציה, אין דיבידנדים.
8. "the environments are not always the same"; "you are essentially competing with HFT firms" ([forum 2801](https://forum.alpaca.markets/t/slippage-paper-trading-vs-real-trading/2801), אומת היום); "some seemingly implausible fill prices" (forum 6602).
9. API; $100K; איפוס "at any time later with arbitrary amount".

**11. TradeStation — Simulated Trading**
1. "Simulated trading may only be done on symbols associated with your real time data entitlements" ([simulated_trading.htm](https://help.tradestation.com/10_00/eng/TradeStationHelp/desktop/simulated_trading.htm), אומת היום).
2. "orders are not actually executed - only simulated executions occur with instant 'fills'" ([sim-vs-live](https://api.tradestation.com/docs/fundamentals/sim-vs-live), אומת היום).
3–7. לא נמצא; בלוג באמינות נמוכה על אופציות ([pickmytrade](https://blog.pickmytrade.io/tradestation-paper-trading-inaccurate-fill-prices/)).
8. חשבון "SIM" (אומת היום). "if you are day trading you will see discrepancies" ([nexusfi 910379](https://nexusfi.com/showthread.php?p=910379)); "If you want to evaluate P/L performance, SIM is definitely not good for that." ([nexusfi 442765](https://nexusfi.com/showthread.php?p=442765)) (לא אומת, 403).
9. File > Manage Simulated Accounts ([manage_sim_accounts](https://help.tradestation.com/09_01/TradeStationHelp/desktop/manage_sim_accounts.htm), אומת היום).

**12. MetaTrader 5 — Demo**
1. "Demo accounts ... feature all the same functionality as the live ones" ([acc_open](https://www.metatrader5.com/en/terminal/help/startworking/acc_open), אומת היום).
2. Instant (requote) / Request / Market / Exchange; FOK / IOC / Return ([performing_deals](https://www.metatrader5.com/en/terminal/help/trading/performing_deals), אומת היום). Tester: "Trading operations are always performed by Bid and Ask prices even if the chart is built by Last prices" ([tick_generation](https://www.metatrader5.com/en/terminal/help/algotrading/tick_generation), אומת היום).
3. מזיכרון (לא אומת, ללא קישור). 4. swap (לא אומת).
5. מרג'ין = "Volume in lots * Contract size * Open market price" × שיעור ([margin_forex](https://www.metatrader5.com/en/terminal/help/trading_advanced/margin_forex), אומת היום); Pepperstone 90%/50% (לא אומת).
6. לא נמצא. 7. לפי ברוקר. 8. "one cannot expect to profit from them" (אומת היום). 9. Market Watch, DOM, Toolbox, Strategy Tester (לא אומת).

**13. אפליקציות "מאמן מניות" ואתרי חינוך**
- **Stock Trainer (A-Life)** ([Play](https://play.google.com/store/apps/details?id=com.alifesoftware.stocktrainer&hl=en_US), אומת היום): "real-time stock data", "20+ world stock markets", Stop-loss/Limit, גרפים 10+ שנים. ביקורות: "I set stop loss at 23.5 while the price was at 25.1. An hour after the market closes I get a notification that my order had been executed. The price did not drop below 25 even for a second."; "Buy limit also is a bit buggy.. The price reaches my limit and it just does not do anything."; "The progress just doesn't seem to be saved."
- **Wall Street Survivor** ([rules](https://www.wallstreetsurvivor.com/rules/), אומת היום): "All orders for the stocks on US exchanges are executed at the real-time bid/ask prices"; בחו"ל 15–20 דק'; מרקט מחוץ לשעות "shortly after the market opens"; "Exploitation of delayed pricing" = פסילה; אופציות קנייה בלבד; תקרות ריכוז ומספר עסקאות.
- **HowTheMarketWorks** ([order-types](https://www.howthemarketworks.com/order-types/), אומת היום): "Quote data is delayed at least 15 minutes". הודעה 2009 ([p=3522](https://www.howthemarketworks.com/?p=3522)): "stock orders had to be delayed by fifteen minutes before execution in order to prevent cheating… Now that the quote delays have been removed… so have the order delays". "closer to the ask/bid" (לא אומת).

### סימולטורים בקוד פתוח

(B2) הקוד נקרא מהדיסק בלבד. כל ה־clone-ים זהים ל־HEAD של ענף ברירת המחדל ב־GitHub היום (`git ls-remote`), וקובצי Lean זהים בייט־לבייט ל־`master` (`cmp`), ולכן מספרי השורות נכונים ל־2026-10-07.

| סימולטור | a. מחיר ביצוע / החלקה / מילוי חלקי | b. Limit/Stop מול נר, וגאפ | c. שורט | d. מינוף / מרג'ין | e. אופציות | f. עמלות ברירת מחדל |
|---|---|---|---|---|---|---|
| **QuantConnect Lean** | Ask/Bid ± slip; ברירת מחדל `NullSlippageModel` (0); יש `VolumeShareSlippageModel` (0.1·share², תקרה 2.5%) ו־`MarketImpactSlippageModel` (Almgren 2005); אין מילוי חלקי לפי נפח במניות | Stop: גאפ לרעה → **Open**−slip, אחרת Stop−slip; Limit: גאפ לטובה → Open | `IShortableProvider`; ברירת מחדל Null (ללא הגבלה, 0 עמלה); ספק IB מקובץ | `SecurityMarginModel`; PDT 4x/2x; margin call: אזהרה 5%, חיסול כשנותר ≤0 ו־used>TPV·1.1 | כן: הקצאה מוקדמת (ITM ≥5%, 4 ימים לפני), מימוש אוטומטי, מרג'ין אופציות | IB: $0.005/מניה, מינ' $1, מקס' 0.5% |
| **zipline-reloaded** | Close של הבר; `FixedBasisPointsSlippage` (5bp, עד 10% מנפח); `VolumeShareSlippage` (0.1·share², 2.5%); מילוי חלקי לפי נפח: כן | טריגר רק מול **Close** (לא H/L); אם המחיר המושפע גרוע מה־limit, אין מילוי | אין | אין | לא | `PerShare` $0.001, מינ' $0 |
| **backtrader** | Open של הבר הבא; `slip_perc`/`slip_fixed` (0; לא על Open אלא `slip_open=True`); מילוי חלקי רק עם `filler` | Stop: גאפ → **Open**, אחרת Stop; Limit: גאפ לטובה → Open | אין בדיקה; `interest` שנתי (0) | `leverage`; `Order.Margin` בשליחה בלבד; אין חיסול | לא | 0 |
| **backtesting.py** | Open של הבר הבא (או Close) × (1±`spread`); הספרד **פעם אחת**; אין החלקה/נפח | Stop לפי H/L; מילוי `max(open, stop)` → בגאפ **Open**; Stop+Limit באותו בר → פסימי | חופשי, בלי עמלה | `margin` (1/leverage), בלי הבחנה initial/maintenance; חיסול רק בהון ≤0 | לא | `commission=0`; `(fixed, relative)` או פונקציה |
| **vectorbt (OSS)** | Close של אותו בר × (1±`slippage`); 0; מילוי חלקי לפי מזומן בלבד | אין Limit; `sl_stop`/`tp_stop`: גאפ → **Open**, אחרת Stop; StopLimit בלי החלקה | אין; מוגבל במזומן | אין | לא | `fees=0`, `fixed_fees=0` |
| **NautilusTrader (Rust)** | מול ספר L1/L2, Ask/Bid; `prob_slippage` (0) טיק אחד לרעה; בר → 4 טיקים O→H→L→C עם חלוקת נפח → מילוי חלקי | Stop בתוך הבר → trigger; גאפ (`fill_at_market`) → **Open**; `prob_fill_on_limit` (1.0) | אסור ב־CASH; אין locate/עמלה | `default_leverage` 10x במרג'ין; `LeveragedMarginModel`; `liquidation_enabled=false` | כן: פקיעה, מימוש ITM, סליקה כספית/פיזית; אין הקצאה מוקדמת | חובה להגדיר (MakerTaker/Fixed/PerContract) |
| **PyBroker** | `PriceType.MIDDLE` של הבר **הבא**; החלקה כבויה; Fixed(5bp)/Volatility(0.1·ATR14)/Volume(0.1·share², 2.5%, חלקי) | Limit מול מחיר המילוי בלבד; Stop מכירה: `min(stop, high)` → בגאפ **High** (אופטימי!) | אין; `interest_rate` על מזומן | `leverage=1.0`; **אין** margin call (מפורש בקוד) | לא | `fee_mode=None`, 0 |
| **Lumibot** | Open; עם ציטוטים Ask/Bid; `TradingSlippage` רק ב־SMART_LIMIT; אין נפח | Stop: גאפ → **Open**, אחרת Stop; Limit: גאפ לטובה → Open | אין | "margin": מזומן שלילי עם ריבית; אין margin call | כן: פקיעה יומית, EXERCISED/ASSIGNED; הקצאה מוקדמת אופציונלית | ללא; `TradingFee(flat, percent, per_contract)` |

**QuantConnect Lean** (אומת היום)
- **a.** `EquityFillModel.MarketFill`: Ask+slip לקנייה, Bid−slip למכירה; עם TradeBar בלבד Ask/Bid = מחיר העסקה האחרון, כלומר אין ספרד אמיתי בלי נתוני ציטוט ([EquityFillModel.cs#L136-L159](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Fills/EquityFillModel.cs#L136-L159)). ברירות מחדל ב־`Security`: `ImmediateFillModel`, `InteractiveBrokersFeeModel`, `NullSlippageModel`, `SecurityMarginModel` ([Security.cs#L354-L359](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/Security.cs#L354-L359)).
```csharp
var slip = asset.SlippageModel.GetSlippageApproximation(asset, order);
fillPrice = GetBestEffortAskPrice(asset, order.Time, ...) + slip;   // Buy
fillPrice = GetBestEffortBidPrice(asset, order.Time, ...) - slip;   // Sell
```
  `VolumeShareSlippageModel(volumeLimit=0.025, priceImpact=0.1)`: `slippagePercent = volumeShare² · priceImpact`, **בלי מילוי חלקי** ([VolumeShareSlippageModel.cs#L37-L92](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Slippage/VolumeShareSlippageModel.cs#L37-L92)).
  `MarketImpactSlippageModel` (Almgren 2005): α=0.891, β=0.600, γ=0.314, η=0.142, δ=0.267; σ מתשואות יומיות, ADV של 10 ימים; רעש גאוסי × U(0,1) ([L30-L172](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Slippage/MarketImpactSlippageModel.cs#L30-L172), σ/ADV: [L238-L254](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Slippage/MarketImpactSlippageModel.cs#L238-L254)). `latency` ברירת מחדל 0.075 שנ' מנפח את `nu`; המחברים ממליצים "to recalibrate" (L40).
```csharp
var nu = (double)order.AbsoluteQuantity / _symbolData.ExecutionTime / _symbolData.AverageVolume;
var permanentImpact = _symbolData.Sigma * _symbolData.ExecutionTime * G(nu) * liquidityAdjustment + SampleGaussian() * noise;
var temporaryImpact = _symbolData.Sigma * H(nu) + SampleGaussian() * noise;
var realizedImpact = temporaryImpact + permanentImpact * 0.5d;   // G(x)=γ·x^α ; H(x)=η·x^β
var ultimateSlippage = (impact * _random.NextDouble()).SafeDecimalCast();
```
- **b.** Stop ([EquityFillModel.cs#L202-L262](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Fills/EquityFillModel.cs#L202-L262)): `if (tradeBar.Low <= order.StopPrice) { if (tradeBar.Open <= order.StopPrice) fill = Open − slip; else fill = Stop − slip; }`. Limit ([L406-L470](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Fills/EquityFillModel.cs#L406-L470)): טריגר `Low < limit`; גאפ לטובה → Open; תמיד מילוי מלא (TODO ב־L437).
- **c.** `IShortableProvider`: `ShortableQuantity`, `FeeRate`, `RebateRate`; `NullShortableProvider` → null ו־0% ([NullShortableProvider.cs#L38-L64](https://github.com/QuantConnect/Lean/blob/master/Common/Data/Shortable/NullShortableProvider.cs#L38-L64)); `LocalDiskShortableProvider` קורא מקובץ יומי ([L60-L100](https://github.com/QuantConnect/Lean/blob/master/Common/Data/Shortable/LocalDiskShortableProvider.cs#L60-L100)).
- **d.** `BuyingPowerModel(leverage)`: initial = maintenance = 1/leverage ([BuyingPowerModel.cs#L92-L106](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/BuyingPowerModel.cs#L92-L106)); `PatternDayTradingMarginModel(2.0, 4.0)` ([L29-L31](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/PatternDayTradingMarginModel.cs#L29-L31)); `DefaultMarginCallModel` עם `marginBuffer=0.10` ([L60-L116](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/DefaultMarginCallModel.cs#L60-L116)). מינוף ברירת מחדל 2x למניות (תיעוד, לא אומת בקוד).
```csharp
if (marginRemaining <= totalPortfolioValue * 0.05m) issueMarginCallWarning = true;
if (marginRemaining <= 0) { if (totalMarginUsed > totalPortfolioValue * (1 + _marginBuffer)) { ... GenerateMarginCallOrders(...) } }
```
- **e.** `DefaultOptionAssignmentModel(0.05, 4 days)` ([L41-L71](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/Option/DefaultOptionAssignmentModel.cs#L41-L71)); `DefaultExerciseModel` לפי `IsAutoExercised(underlying.Close)` ([L34-L72](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/OptionExercise/DefaultExerciseModel.cs#L34-L72)); `OptionMarginModel` 10% / 20% OTM ([L32-L33](https://github.com/QuantConnect/Lean/blob/master/Common/Securities/Option/OptionMarginModel.cs#L32-L33)).
- **f.** IB: `feePerShare: 0.005, minimumFee: 1, maximumFeeRate: 0.005` ([InteractiveBrokersFeeModel.cs#L150-L173](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Fees/InteractiveBrokersFeeModel.cs#L150-L173)); אופציות $0.25–$0.70 לחוזה, מינ' $1 ([L285-L317](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Fees/InteractiveBrokersFeeModel.cs#L285-L317)).

**zipline-reloaded** (אומת היום, `main`)
- **a.** `SimulationBlotter`: `FixedBasisPointsSlippage()` + `PerShare()` ([simulation_blotter.py#L63-L77](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/blotter/simulation_blotter.py#L63-L77)); `FixedBasisPointsSlippage(basis_points=5.0, volume_limit=0.1)` ([slippage.py#L615-L680](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L615-L680)); תמיד **close** של הבר ([L160-L169](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L160-L169)). `VolumeShareSlippage` ([L241-L331](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L241-L331)):
```python
#   price * (1 + price_impact * (volume_share ** 2))   ; volume_limit=0.025, price_impact=0.1
max_volume = self.volume_limit * volume
volume_share = min(total_volume / volume, self.volume_limit)
simulated_impact = (volume_share**2 * math.copysign(self.price_impact, order.direction) * price)
```
  הזמנה של 2.5% מהנפח → השפעה 0.1·0.025² = 0.00625%, כמעט אפס. **לא מציאותי למניות קטנות.** יתרה → בר הבא; `EODCancel` מבטל בסוף היום ([cancel_policy.py#L46](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/cancel_policy.py#L46)). `VolatilityVolumeShare` (חוזים): `MI = eta·sigma·sqrt(psi)`, `DEFAULT_ETA=0.049` ([L520-L600](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L520-L600), [constants.py#L106](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/constants.py#L106)).
- **b.** טריגר מול `current_price` = close בלבד ([order.py#L160-L215](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/order.py#L160-L215), [slippage.py#L184](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L184)); `fill_price_worse_than_limit_price` ([L48-L78](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L48-L78)).
- **c/d/e.** אין. **f.** `DEFAULT_PER_SHARE_COST=0.001`, מינימום 0, `PER_DOLLAR=0.0015` ([commission.py#L25-L29](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/commission.py#L25-L29)).

**backtrader** (אומת היום, חלקו דרך סוכן משנה)
- **a.** פרמטרים ([bbroker.py#L224-L241](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L224-L241)); מרקט ב־Open הבא ([L853-L870](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L853-L870)); החלקה נחתכת ל־H/L ([L994-L1038](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L994-L1038)); `filler` ([fillers.py#L30-L111](https://github.com/mementum/backtrader/blob/master/backtrader/fillers.py#L30-L111)): `maxsize = (order.data.volume[ago] * self.p.perc) // 100`.
- **b.** ([L921-L944](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L921-L944)):
```python
if popen >= pcreated:   # price penetrated with an open gap - use open
    p = self._slip_up(phigh, popen, doslip=self.p.slip_open)
elif phigh >= pcreated: # penetrated during the session - use trigger price
    p = self._slip_up(phigh, pcreated)
```
  Limit ([L898-L919](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L898-L919)).
- **c.** `interest` ([comminfo.py#L122-L130](https://github.com/mementum/backtrader/blob/master/backtrader/comminfo.py#L122-L130), חישוב [L274-L305](https://github.com/mementum/backtrader/blob/master/backtrader/comminfo.py#L274-L305), ניכוי [bbroker.py#L1184-L1193](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L1184-L1193)). **d.** `getsize` ([comminfo.py#L192-L197](https://github.com/mementum/backtrader/blob/master/backtrader/comminfo.py#L192-L197)); `Order.Margin` ([bbroker.py#L558-L583](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L558-L583)). **f.** ([comminfo.py#L229-L237](https://github.com/mementum/backtrader/blob/master/backtrader/comminfo.py#L229-L237)).

**backtesting.py** (אומת היום)
- **a.** `Backtest(..., spread=.0, commission=.0, margin=1., trade_on_close=False, ...)` ([L1197-L1207](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L1197-L1207)); ספרד ([L838-L843](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L838-L843)): `return (price or self.last_price) * (1 + copysign(self._spread, size))`. לפי התיעוד ([L1139-L1157](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L1139-L1157)) `spread` מוחל פעם אחת → להזין את **כל** הספרד. חריגה ממרג'ין → ביטול ([L1006-L1012](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L1006-L1012)).
- **b.** ([L888-L921](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L888-L921)):
```python
is_stop_hit = ((high >= stop_price) if order.is_long else (low <= stop_price))
price = prev_close if self._trade_on_close and not order.is_contingent else open
if stop_price: price = max(price, stop_price) if order.is_long else min(price, stop_price)
```
- **d.** `self._leverage = 1 / margin` ([L748](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L748)); חיסול בהון ≤0 ([L866-L873](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L866-L873)). **f.** ([L733-L760](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L733-L760)).

**vectorbt (OSS)** (אומת היום)
- **a.** ברירות מחדל ([_settings.py#L383-L416](https://github.com/polakowo/vectorbt/blob/master/vectorbt/_settings.py#L383-L416)); `adj_price = price * (1 + slippage)` ([nb.py#L90](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L90)), עמלות ([L148-L150](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L148-L150)).
- **b.** SL/TP ([nb.py#L1861-L1889](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L1861-L1889)): `if open <= stop_price: return open; if low <= stop_price <= high: return stop_price`; StopLimit בלי החלקה ([L1616-L1630](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L1616-L1630)). **c.** שורט לפי מזומן ([L239-L257](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L239-L257)).

**NautilusTrader** (אומת היום, `develop`, מנוע Rust; ME = `crates/execution/src/matching_engine/mod.rs`)
- **a.** `book.simulate_fills` ([ME#L4641-L4656](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L4641-L4656)); `DefaultFillModel::default()` = `(1.0, 0.0, None)` ([fill.rs#L316-L320](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/models/fill.rs#L316-L320)); החלקה טיק אחד ב־L1 ([ME#L5275-L5279](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L5275-L5279)); מודלים נוספים ([fill.rs#L361-L1401](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/models/fill.rs#L361-L1401)); `BarTickSizes::from_volume` ([ME#L7668-L7730](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L7668-L7730)).
- **b.** O→H→L→C ([ME#L1966-L2037](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L1966-L2037)); גאפ ([ME#L1987-L1990](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L1987-L1990), [L4658-L4670](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L4658-L4670)); `prob_fill_on_limit` ([ME#L4945-L4970](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L4945-L4970)).
```rust
} else if self.core.last.is_some_and(|last| bar.open != last) {
    // Gap between previous close and this bar's open
    self.fill_at_market = true;
```
- **c.** ([ME#L3253-L3291](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/mod.rs#L3253-L3291)). **d.** 10x ([config.rs#L281-L285](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/backtest/src/config.rs#L281-L285)); חיסול ([config.rs#L359-L367](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/backtest/src/config.rs#L359-L367), [exchange.rs#L1793-L1830](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/backtest/src/exchange.rs#L1793-L1830)). **e.** ([settlement.rs#L40](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/execution/src/matching_engine/settlement.rs#L40)). **f.** ([config.rs#L294-L299](https://github.com/nautechsystems/nautilus_trader/blob/develop/crates/backtest/src/config.rs#L294-L299)).

**PyBroker** (אומת היום, 2.0.1)
- **a.** `PriceType.MIDDLE` ([context.py#L1645-L1654](https://github.com/edtechre/pybroker/blob/master/src/pybroker/context.py#L1645-L1654)); `VolumeSlippageModel` ([slippage.py#L315-L440](https://github.com/edtechre/pybroker/blob/master/src/pybroker/slippage.py#L315-L440)): `impact = self._price_impact * ratio * ratio`; Fixed/Volatility ([L171-L312](https://github.com/edtechre/pybroker/blob/master/src/pybroker/slippage.py#L171-L312)).
- **b.** Limit ([portfolio.py#L1019-L1020](https://github.com/edtechre/pybroker/blob/master/src/pybroker/portfolio.py#L1019-L1020)); Stop ([L2075-L2078](https://github.com/edtechre/pybroker/blob/master/src/pybroker/portfolio.py#L2075-L2078)): `if low <= stop_value: return min(stop_value, high)` → בגאפ **High**, אופטימי, לא להעתיק.
- **c/d.** "an adverse mark cannot be enforced without a margin call, which is not modeled" ([L894-L905](https://github.com/edtechre/pybroker/blob/master/src/pybroker/portfolio.py#L894-L905)). **f.** ([config.py#L98-L101](https://github.com/edtechre/pybroker/blob/master/src/pybroker/config.py#L98-L101)).

**Lumibot** (אומת היום, `dev`; BB = `lumibot/backtesting/backtesting_broker.py`)
- **a.** `price = open` ([BB#L3074-L3075](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L3074-L3075)); Ask/Bid עם ציטוטים ([BB#L4243-L4245](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L4243-L4245)).
- **b.** ([BB#L4972-L4995](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L4972-L4995)): `if side == "sell" and stop_val >= open_val: return open_val; if low_val <= stop_val <= high_val: return stop_val`; Limit ([BB#L4932-L4970](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L4932-L4970)).
- **d.** `TYPICAL_FUTURES_MARGINS` ([BB#L34-L130](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L34-L130)). **e.** `settle_expired_option_contract` ([BB#L1704-L1784](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L1704-L1784)); הקצאה מוקדמת ([BB#L202-L215](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/backtesting/backtesting_broker.py#L202-L215)). **f.** [trading_fee.py](https://github.com/Lumiwealth/lumibot/blob/dev/lumibot/entities/trading_fee.py).

### נוסחאות להערכת ספרד והחלקה מנתונים יומיים

סימונים: O,H,L,C מחירי פתיחה/גבוה/נמוך/סגירה; o,h,l,c הלוגריתמים; V נפח יומי; ADV נפח ממוצע; σ סטיית תקן של תשואה יומית; S ספרד יחסי. כל המאמרים אומתו היום מול Crossref דרך DOI.

**1. Roll (1984)** — קו־וריאנס סדרתית. קלט: סגירות בלבד. חלון 21–252 ימים. מגבלה: קו־וריאנס חיובית → לא מוגדר (במניות קטנות קורה הרבה; "כשליש עד חצי מהחלונות" לא אומת). מקור: [doi:10.1111/j.1540-6261.1984.tb03897.x](https://doi.org/10.1111/j.1540-6261.1984.tb03897.x). קוד: [bidask/r/R/roll.R#L5-L23](https://github.com/eguidotti/bidask/blob/main/r/R/roll.R#L5-L23).
```
S = 2 · sqrt( −Cov(Δc_t, Δc_{t−1}) )          # אם Cov > 0: לאפס או signed root
R: s2 <- -4 * n/(n - 1) * (m[,3] - m[,1]*m[,2])
```

**2. Corwin & Schultz (2012)** — גבוה–נמוך. קלט: H,L,C. חלון: ממוצע ~21 יום של אומדנים דו־יומיים. מגבלה: מוטה מעלה בתנודתיות גבוהה ובמסחר דליל; הרבה ערכים שליליים (לאפס, "CS2"). מקור: [doi:10.1111/j.1540-6261.2012.01729.x](https://doi.org/10.1111/j.1540-6261.2012.01729.x). קוד: [bidask/r/R/cs.R#L12-L45](https://github.com/eguidotti/bidask/blob/main/r/R/cs.R#L12-L45).
```
β = (h_t−l_t)² + (h_{t−1}−l_{t−1})²
γ = (max(H_t,H_{t−1}) − min(L_t,L_{t−1}))²   (בלוג)
α = (√(2β) − √β)/(3−2√2) − √(γ/(3−2√2))
S = 2(e^α − 1)/(1 + e^α)
R: gap <- pmax(0, c1 - h) + pmin(0, c1 - l)     # תיקון overnight
   b <- (h - l)^2 + (h1 - l1)^2 ; g <- (pmax(ah, h1) - pmin(al, l1))^2
   a <- (sqrt(2*b) - sqrt(b)) / (3 - 2*sqrt(2)) - sqrt(g / (3 - 2*sqrt(2)))
   s <- 2*(exp(a) - 1) / (1 + exp(a))
```

**3. Abdi & Ranaldo (2017)** — סגירה–גבוה–נמוך. קלט: H,L,C. חלון ~21 יום. פחות רגיש לתנודתיות מ־CS. מקור: [doi:10.1093/rfs/hhx084](https://doi.org/10.1093/rfs/hhx084). קוד: [bidask/r/R/ar.R#L12-L39](https://github.com/eguidotti/bidask/blob/main/r/R/ar.R#L12-L39).
```
η_t = (h_t + l_t)/2
S² = 4 · E[(c_t − η_t)(c_t − η_{t+1})] ;  S = sqrt(max(0, S²))     # "AR2": שורש לכל יומיים ואז ממוצע
R: s2 <- 4 * (c1 - m1) * (c1 - m2)
```

**4. Ardia, Guidotti & Kroencke (2024): EDGE** — משלב אומדני מומנטים מ־O,H,L,C עם משקלות לפי שונות ותיקון להסתברות שאין מסחר. קלט: OHLC, מינימום 3 תצפיות; חלון מומלץ 21 ימים כל עוד ≥2 עסקאות לתקופה; למניות שנסחרות פחות מפעם ביום → נרות שבועיים (README). מקור: [doi:10.1016/j.jfineco.2024.103916](https://doi.org/10.1016/j.jfineco.2024.103916) (SSRN 3892335 החזיר 403, לא אומת). קוד: [bidask/python/bidask/edge.py#L40-L107](https://github.com/eguidotti/bidask/blob/main/python/bidask/edge.py#L40-L107) (`pip install bidask`, MIT, כולל `edge_rolling`; ב־R: `spread(x, method=c("EDGE","AR","CS","ROLL"))`). נתונים מוכנים לכל CRSP: [doi:10.7910/DVN/YAY4H6](https://doi.org/10.7910/DVN/YAY4H6).
```
m = (h+l)/2
r1 = m_t−o_t ; r2 = o_t−m_{t−1} ; r3 = m_t−c_{t−1} ; r4 = c_{t−1}−m_{t−1} ; r5 = o_t−c_{t−1}
x1 = −4/p_o·d1·r2 − 4/p_c·d3·r4 ;  x2 = −4/p_o·d1·r5 − 4/p_c·d5·r4
S² = (v2·E[x1] + v1·E[x2])/(v1+v2) ;  S = sqrt(|S²|)
python: s2 = (v2*e1 + v1*e2) / vt if vt > 0 else (e1 + e2) / 2.
```

**5. Amihud (2002) ו־Kyle's λ (1985)** — Amihud: תשואה מוחלטת חלקי מחזור בדולרים; proxy **לינארי** להשפעה, מגזים בהזמנות גדולות; עדיף לדירוג נזילות או לכיול Y. מקור: [doi:10.1016/S1386-4181(01)00024-6](https://doi.org/10.1016/S1386-4181(01)00024-6). Kyle: ΔP = λ·(זרימת הזמנות נטו), λ = σ_v/(2σ_u); אמפירית דורש נתונים תוך־יומיים. מקור: [doi:10.2307/1913210](https://doi.org/10.2307/1913210). קוד ייעודי: לא נמצא (שורת pandas, לא אומת).
```
ILLIQ = (1/D) · Σ_d |r_d| / (P_d · V_d)      (×10⁶ מקובל)
ΔP/P ≈ ILLIQ · (Q · P)
```

**6. חוק השורש הריבועי ו־Almgren–Chriss**
- חוק השורש: Y בסדר גודל 1 (0.5–1 בספרות, **לא אומת במדויק**). דוגמה: 10% מהנפח, σ=4%, Y=0.7 → 0.7·4%·0.316 ≈ 0.9%. מקורות: Tóth et al. 2011 [doi:10.1103/PhysRevX.1.021006](https://doi.org/10.1103/PhysRevX.1.021006) / [arXiv:1105.1694](https://arxiv.org/abs/1105.1694); Gatheral 2010 [doi:10.1080/14697680903373692](https://doi.org/10.1080/14697680903373692).
- Almgren, Thum, Hauptmann & Li (2005), *Direct Estimation of Equity Market Impact* ([PDF, upenn](https://www.cis.upenn.edu/~mkearns/finread/costestim.pdf); הקישור ב־Lean ל־ram-ai.com → 404). γ=0.314, η=0.142, α=0.891, β=0.600, δ=0.267. **המאמר דוחה שורש ריבועי לטובת חזקה 3/5, ומבוסס על large-cap בלבד** → לכייל מחדש למניות קטנות.
- Almgren & Chriss (2000/2001) [doi:10.21314/JOR.2001.041](https://doi.org/10.21314/JOR.2001.041): השפעה קבועה g(v)=γ·v, זמנית h(v)=ε·sgn(v)+η·v, ε = חצי ספרד + עמלה (הצורה הלינארית מזיכרון, לא אומת מול הטקסט).
```
I(Q) ≈ Y · σ_daily · sqrt(Q / V_daily)                       # חוק השורש
Almgren 2005:  I = γ·σ·(X/V)·(Θ/V)^{1/4}   (קבוע)
               J = I/2 + sgn(X)·η·σ·|X/(V·T)|^{3/5}   (זמני; J = העלות המשולמת)
```

**7. הכללים המעשיים בסימולטורים**

| מודל | נוסחה | ברירות מחדל | קישור |
|---|---|---|---|
| zipline `VolumeShareSlippage` | P·(1 ± 0.1·min(q/V_bar, 0.025)²); כמות לבר ≤ 0.025·V_bar | 0.1, 0.025 | [slippage.py#L241-L331](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L241-L331) |
| zipline `FixedBasisPointsSlippage` | P·(1 ± 0.0005); כמות ≤ 0.1·V_bar | 5bp, 10% | [L615-L680](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L615-L680) |
| zipline `VolatilityVolumeShare` | P·(1 ± η·σ_ann·√(q/ADV20)/10⁴) | η=0.049 | [L520-L600](https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/finance/slippage.py#L520-L600) |
| Lean `VolumeShareSlippageModel` | slip$ = 0.1·min(q/V_bar, 0.025)²·P, בלי מילוי חלקי | 0.025, 0.1 | [L37-L92](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Slippage/VolumeShareSlippageModel.cs#L37-L92) |
| Lean `MarketImpactSlippageModel` | Almgren 2005: temp + perm/2 + רעש, × U(0,1) | α .891 β .6 γ .314 η .142 δ .267 | [L30-L172](https://github.com/QuantConnect/Lean/blob/master/Common/Orders/Slippage/MarketImpactSlippageModel.cs#L30-L172) |
| backtesting.py `spread` | P·(1 ± spread), פעם אחת | 0 | [L838-L843](https://github.com/kernc/backtesting.py/blob/master/backtesting/backtesting.py#L838-L843) |
| backtrader `slip_perc` | P·(1 ± slip_perc), נחתך ל־H/L | 0 | [bbroker.py#L994-L1038](https://github.com/mementum/backtrader/blob/master/backtrader/brokers/bbroker.py#L994-L1038) |
| vectorbt `slippage` | P·(1 ± slippage) | 0 | [nb.py#L90](https://github.com/polakowo/vectorbt/blob/master/vectorbt/portfolio/nb.py#L90) |

**8. המתכון המעשי (מניה קטנה, נתונים יומיים בלבד)**
```
1. Spread:  S = EDGE(O,H,L,C) על חלון 21 ימים (bidask.edge_rolling, sign=True, שלילי→0).
   גיבוי/בקרה: Abdi–Ranaldo (AR2). רצפה: S ≥ max(S_EDGE, 2·tick/P)  (למשל $0.01×2/P).
   אם V נמוך מאוד (< ~2 עסקאות/יום) → להעריך על נרות שבועיים.
2. σ = סטיית התקן של log(C_t/C_{t−1}) על 20 ימים; ADV = ממוצע V על 20 ימים.
3. עלות כוללת (חד־כיווני):  cost% = S/2 + Y·σ·sqrt(Q/ADV),  Y≈0.7 (טווח 0.5–1; חוק השורש, Tóth 2011/Almgren 2005).
4. מחיר מילוי: קנייה = Open_next·(1+cost%), מכירה = Open_next·(1−cost%); לחסום בתוך [Low, High] של הבר.
5. מגבלת השתתפות: למלא לכל היותר 10% מ־V של הבר (zipline FixedBP=10%; שמרני: 2.5%); היתרה → בר הבא או ביטול בסוף היום.
6. Stop (מכירה): אם Open ≤ Stop → מילוי ב־Open·(1−cost%); אחרת אם Low ≤ Stop → Stop·(1−cost%). (Lean/backtrader/backtesting.py)
7. Limit (קנייה): ממולא רק אם Low < Limit (קפדני: Low ≤ Limit−S/2·P); מחיר = min(Open, Limit); גם כאן ≤10% נפח.
8. בקרת שפיות: Amihud ILLIQ גבוה (top decile) → להעלות Y או להוריד את מגבלת ההשתתפות.
```

### מסכים וכלים שחוזרים אצל כולם
1. **רשימת מעקב** אצל כמעט כולם (חריג: Investopedia, שמבקרים מציינים לרעה).
2. **טיקט פקודה:** Market/Limit/Stop/Stop-Limit עם Day/GTC; ברוקרים גם OCO/Bracket/Trailing; כל פלטפורמה מפרסמת פקודות שלא נתמכות בדמו (IBKR: VWAP/Auction; TOS: MOO/MOC).
3. **פוזיציות + רווח/הפסד** ממומש ולא ממומש, מזומן וכוח קנייה; עם מינוף גם מצב מרג'ין (TradingView Account Manager, TOS Monitor).
4. **היסטוריית פקודות וביצועים** נפרדת לפתוחות, שבוצעו ובוטלו.
5. **מסחר מתוך הגרף** (TradingView, NinjaTrader), DOM/SuperDOM במתקדמות.
6. **איפוס יתרה / הון התחלתי:** IBKR $1M (ועד פי 5 מהאמיתי); TOS, Webull, Alpaca, eToro $100K; Trading 212 איפוס בלבד; eToro רק דרך תמיכה.
7. **סימון ויזואלי של דמו:** TOS כתום/כחול, כפתורים כתומים ב־Webull, רקע צבעוני ב־NinjaTrader, חשבונות "D-"/"SIM".
8. **לוח מובילים ומשחקים עם חוקים** רק בחינוכיות (Investopedia, MarketWatch, WSS, HTMW): יוצר המשחק קובע עמלה, מרג'ין, שורט, אופציות, ריבית, תקרות.
9. **הגדרות ריאליזם למשתמש:** עמלה (TradingView), מינוף לפי נכס (TradingView), מתגי מילוי מיידי/חלקי (NinjaTrader), סוג מרג'ין (TOS).

### מה כדאי להעתיק
1. **קנייה ב־Ask ומכירה ב־Bid, גרף ב־mid/last** (TradingView, Wall Street Survivor, MT5). במניות קטנות עם ספרד רחב זה עיקר העלות; כשאין ציטוטים, להעריך את הספרד ב־EDGE (חבילת `bidask`, MIT).
2. **מילוי לפי נזילות עם יתרה לבר הבא:** Alpaca מודה שהדמו שלה נותן מילוי "much larger than the actual available liquidity"; לתקן עם מגבלת השתתפות (`volume_limit` של zipline, `FixedBarPerc` של backtrader) ומנוע לפי מחזור בסגנון NinjaTrader.
3. **מילוי חלקי לפי מחזור אמיתי**, לא אקראי (Alpaca 10%).
4. **מבנה עלות "חצי ספרד + השפעה"** (ε ו־η של Almgren–Chriss), עם השפעה בחוק השורש σ·√(Q/ADV), ולא 0.1·share² של zipline/Lean שנותן כמעט אפס.
5. **כלל הגאפ של Lean/backtrader/backtesting.py:** סטופ שנפרץ בגאפ מתמלא ב־**Open** (או גרוע ממנו), לא במחיר הסטופ; Limit בגאפ לטובה ב־Open. זה מה שקורה בחשבונות אמיתיים (eToro, Trading 212, FOS $31.49→$60.01), ואף דמו לא מתעד זאת, לכן להציג למשתמש במפורש.
6. **החלקה מוגדרת ומוגבלת לטווח האמיתי** (NinjaTrader: בטיקים, רק על Market/Stop/MIT, בתוך הנר הבא), כדי שלא ייווצרו מחירים "בלתי אפשריים".
7. **עמלות וריביות פעילות כברירת מחדל:** עמלת IB של Lean ($0.005/מניה, מינ' $1, מקס' 0.5%); ריבית מרג'ין (MarketWatch, backtrader `interest`); עמלת השאלה בסגנון `IShortableProvider` של Lean ו־Alpaca האמיתי (ETB חינם, HTB עמלה יומית ולפעמים לא זמין; טווח 3–30% לשנה לא אומת).
8. **מרג'ין אמיתי:** דחיית פקודה בלי מרג'ין (TradingView); אזהרה ב־5% וחיסול כשנותר ≤0 (Lean) במקום "חיסול רק באפס הון" (backtesting.py); מחיר מינימלי למניה ממונפת (WSS).
9. **ההנחה הפסימית של backtesting.py:** Stop ו־Limit באותו נר → התרחיש הגרוע.
10. **סימון חד של מצב סימולציה, איפוס פשוט, ויומן ביצועים** שמראה לכל עסקה את המחיר שנראה במסך מול מחיר הביצוע. העמודה הזו לא קיימת באף פלטפורמה.

### טעויות שכדאי להימנע מהן
1. **מילוי מיידי במחיר המסך** (TradeStation "instant fills", TOS "instant fills all the time"). הסיבה המרכזית ש"הדמו טוב מהמציאות".
2. **לימיט שמתמלא בנגיעה ראשונה בלי תור** (NinjaTrader "filled too easily on touch"; TradingView "15–25% better than live" לפי מתחרה). לימיט צריך להתמלא רק כשהמחיר **עובר** את הרמה או שהיה מספיק מחזור בה.
3. **בלי מגבלת נזילות:** "המיליארדרים" של Investopedia ($100K→$500B במניות פני); Alpaca בלי בדיקת כמות. הורס גם לוח מובילים.
4. **מחירים שלא היו בשוק:** IBKR לימיט 7.97 מולא ב־8.13; Alpaca $17.52 כשהמחיר לא ירד מ־$18; Stock Trainer סטופ בלי שהמחיר הגיע. לבדוק שכל מילוי בתוך [Low, High] של פרק הזמן.
5. **ביצוע ב־Close של אותו נר שבו נוצר האות** (ברירת המחדל של vectorbt ו־zipline): הטיית מבט־קדימה; במניה קטנה Close של היום שונה מאוד מ־Open של מחר.
6. **כלל סטופ אופטימי:** בדיקה רק מול Close (zipline) או מילוי בגאפ ב־`min(stop, high)` (PyBroker). למלא ב־Open.
7. **דמו בלי עמלות, ריביות ועמלות השאלה** (eToro "does not incur any fees", Alpaca בלי עמלות ודיבידנדים, TradingView עמלה כבויה) → רווח מנופח. ובלי הגבלת שורט (backtesting.py, vectorbt): במניות קטנות השאלה לא זמינה או יקרה.
8. **פער מידע במחירים מושהים בלי הגנה:** Investopedia ו־HTMW נאלצו להוסיף השהיית ביצוע וחוקים נגד "Exploitation of delayed pricing". עם נתונים מושהים, הביצוע חייב להיות לפי מחיר מאוחר מרגע השליחה.
9. **אומדני ספרד בלי טיפול:** Roll/CS על יום אחד או בלי איפוס שליליים; להשתמש ב־EDGE או ב־CS2/AR2 על 21 ימים. ולהחיל חצי ספרד בכל כיוון (ב־backtesting.py `spread` מוחל פעם אחת → להזין ספרד מלא). ולא להעתיק את מקדמי Almgren 2005 כמו שהם למניות קטנות (large-cap בלבד; ב־Lean גם latency 75ms ואקראיות U(0,1)).
10. **ממשק שמבלבל בין דמו לאמיתי, ואובדן נתונים** (Trading 212 "you could get the 2 mixed up", Webull מוחק אחרי 180 יום, Stock Trainer "progress … not saved"), והתנהגות לא מתועדת (אף פלטפורמה לא מתעדת סטופ בפער, החלקה או חיסול בדמו). לשמור עמיד ולפרסם עמוד "איך מחושב המילוי".

## מה אומת היום ומה לא

| פריט | סטטוס | קישור |
|---|---|---|
| Yahoo chart v8 AAPL 1m — בדיקת העורך 17:02:28Z (HTTP 200, נר אחרון = זמן הבקשה) | ✅ אומת היום | https://query1.finance.yahoo.com/v8/finance/chart/AAPL?interval=1m&range=1d |
| Yahoo `range=max` מחזיר 3mo (169 נרות); `range=5y` מחזיר 1d (1,255) — העורך 17:03:42Z | ✅ אומת היום (מכריע A4 מול A3) | https://query2.finance.yahoo.com/v8/finance/chart/AAPL?interval=1d&range=max |
| Yahoo `query1` 429 בקריאה ראשונה (A4) מול 200 (A3, העורך) | ⚠️ לא הוכרע | — |
| Yahoo איחור TASE: שדה 15 (A3) מול טבלה 20 דק' | ⚠️ לא הוכרע | https://help.yahoo.com/kb/SLN2310.html |
| Stooq CSV `q/l` → 404; `q/d/l` → אתגר JS — העורך 17:02:30Z | ❌ נכשל (3 בדיקות) | https://stooq.com/q/l/?s=aapl.us&f=sd2t2ohlcv&h&e=csv |
| Alpha Vantage GLOBAL_QUOTE IBM `apikey=demo` → 221.29, latest trading day 2026-10-06 — העורך 17:02:31Z | ✅ אומת היום (סוף יום, לא זמן אמת) | https://www.alphavantage.co/query?function=GLOBAL_QUOTE&symbol=IBM&apikey=demo |
| Twelve Data `demo` AAPL 336.11, איחור ~דקה | ✅ אומת היום (A1) | https://twelvedata.com/pricing |
| EODHD `demo` AAPL 335.42, איחור 15 דק' | ✅ אומת היום (A1) | https://eodhd.com/financial-apis/live-realtime-stocks-api |
| dxFeed demo AAPL 335.70 + bid/ask, 15 דק' | ✅ אומת היום (A2) | https://kb.dxfeed.com/en/getting-started.html |
| Nasdaq.com bid/ask 336.11/336.16, 10 שנות היסטוריה, marketmovers | ✅ אומת היום (A3) | https://www.nasdaq.com/legal |
| Cboe delayed JSON, 3,568 חוזים עם Greeks, 15 דק' | ✅ אומת היום (A3, A4) | https://www.cboe.com/terms |
| TradingView scanner `delayed_streaming_900` | ✅ אומת היום (A3) | https://www.tradingview.com/policies/ |
| Finviz "delayed by 1 minute"; Elite $39.50 | ✅ אומת היום (A3) | https://finviz.com/elite |
| Google Finance scrape; TASE 20 דק' | ✅ אומת היום (A3) | https://www.google.com/googlefinance/disclaimer/ |
| CNBC `quote.htm` זמן אמת + CORS `*`; `restQuote.htm` 404 | ✅/❌ אומת היום (A3) | — |
| `api.tase.co.il` TEVA 11990 אג', historyeod | ✅ אומת היום (A4) | — |
| Börse Frankfurt SAP 188.02; אי־התאמה מול Yahoo 187.62 | ✅ עובד / ⚠️ לא הוכרע (A4) | — |
| Yahoo options v7 (crumb), 23 פקיעות, 15 דק' | ✅ אומת היום (A4) | — |
| Yahoo screener day_gainers | ✅ אומת היום (A3, A4) | — |
| Alpha Vantage HISTORICAL_OPTIONS IBM demo (2,112 חוזים, 2026-10-06) | ✅ אומת היום (A1, A4) | https://www.alphavantage.co/documentation/ |
| Alpaca: תיעוד Basic/IEX/200 לדקה/Paper Only "Anyone globally"/ToS | ✅ תיעוד אומת היום; API 📄 (401) | https://docs.alpaca.markets/docs/paper-trading |
| Alpaca movers פתוח ל־Basic? | לא אומת | https://docs.alpaca.markets/reference/movers-1 |
| Tradier sandbox דורש חשבון ברוקראז'? | ⚠️ לא הוכרע (תיעוד נוכחי מול כתבות 2014) | https://docs.tradier.com/docs/market-data.md |
| IBKR: חשבון ממומן נדרש; $500 מינימום לנתונים | ✅ אומת היום (A2) | https://www.interactivebrokers.com/en/pricing/market-data-pricing.php |
| Databento: כרטיס אשראי חובה | ✅ אומת היום (A2) | https://databento.com/blog/why-payment-information-required |
| Databento Standard מחיר; Finnhub מגבלות/תמחור; FMP כל הדפים (403); Intrinio/Barchart | לא אומת | — |
| Massive תמחור מניות ואופציות ($0 / $29 / $79) | ✅ אומת היום (A1, A4) | https://massive.com/pricing |
| Massive = Polygon.io (שינוי שם) | לא אומת באתר הרשמי; שני הדומיינים עונים זהה | https://fisd.net/polygon-io-is-now-massive |
| Tiingo: IEX רק ב־Power; אין CORS | ✅ אומת היום (A1) | https://www.tiingo.com/about/pricing |
| EODHD כולל TASE? | לא אומת | https://eodhd.com/pricing |
| TASE DataHub: פורטל סגור (401, OIDC); 4 מוצרים חינמיים | ✅ סגירה אומתה; מוצרים לא אומת (tasepy) | https://pypi.org/project/tasepy/ |
| moomoo: ישראל לא נתמכת | לא אומת | https://www.moomoo.com/us/learn/moomoo-available-countries |
| Cboe All Access: כרטיס אשראי לניסיון | ✅ אומת היום (A4); מחיר Tier 1 לא אומת | https://datashop.cboe.com/cboe-all-access-api |
| Schwab/Webull דרישות חשבון | Webull ✅ (FAQ); Schwab לא אומת (מקורות משניים) | https://developer.webull.com/apis/docs/faq |
| B1: IBKR paper top-of-book, $1M, "stops always simulated" | ✅ אומת היום | https://ibkrguides.com/clientportal/aboutpapertradingaccounts.htm |
| B1: TradingView Ask/Bid, עמלה כבויה, מרג'ין | ✅ אומת היום | https://www.tradingview.com/support/solutions/43000647247 |
| B1: Alpaca paper NBBO, 10% חלקי, בלי בדיקת נזילות | ✅ אומת היום | https://docs.alpaca.markets/docs/paper-trading |
| B1: NinjaTrader מתגי מילוי; החלקה בטיקים | ✅ אומת היום | https://static.ninjatrader.com/support/helpGuides/nt8/options_trading.htm |
| B1: Webull FAQ, WSS rules, HTMW, MT5, TradeStation | ✅ אומת היום | https://www.wallstreetsurvivor.com/rules/ |
| B1: Schwab paperMoney "theoretical fill", Investopedia FAQ, MarketWatch מצב האתר, IBKR KB | לא אומת (403/401) | https://www.schwab.com/content/thinkorswim-papermoney-stock-trading-simulator |
| B1: ציטוטי משתמשים מסומנים (לא אומת) ב־reddit/futures.io/nexusfi/tapeboard/coincub | לא אומת | — |
| B1: טענות "מזיכרון" (IBKR שורט/עמלות בנייר, MT5 סטופ בפער, T212 stop-out 50%) | לא אומת, ללא קישור | — |
| B2: כל קטעי הקוד (Lean, zipline, backtrader, backtesting.py, vectorbt, Nautilus, PyBroker, Lumibot) | ✅ אומת היום מול HEAD | https://github.com/QuantConnect/Lean |
| B2: Lean BrokerageModel מציב EquityFillModel; מינוף 2x ברירת מחדל | לא אומת (קובץ לא על הדיסק) | — |
| B2: Roll, CS, AR, EDGE, Amihud, Kyle, Tóth, Gatheral, Almgren 2005, Almgren–Chriss — DOI | ✅ אומת היום מול Crossref | https://doi.org/10.1016/j.jfineco.2024.103916 |
| B2: Y≈0.7 (0.5–1), ספרד 1–5% במניות קטנות, עמלת HTB 3–30%, צורת h(v) הלינארית, "שליש–חצי מהחלונות" ב־Roll | לא אומת | — |

## נספח — חלקי העובדים

| קובץ | מצב | גודל | פערים שנמצאו |
|---|---|---|---|
| `parts/A1.md` — ספקים עם מפתח (ארה"ב) | קיים | 24,990 בייט | Finnhub: מגבלות ותמחור לא אומתו (JS), טענת 60/דקה ללא קישור. FMP: כל הדפים 403, בדיקה ❌. Massive/Tiingo/Marketstack: אין demo → 📄. דפי הרשמה לא נבדקו לעומק (רק Twelve Data ו־AV). |
| `parts/A2.md` — ברוקרים | קיים | 29,807 בייט | אין מפתחות לאף ברוקר → רובם 📄 עם שגיאת 401 בלבד. Tradier: סתירה בין תיעוד 2026 לכתבות 2014 לא הוכרעה. Databento Standard מחיר, זמינות לישראל ב־Tradier/Schwab/Webull, שדות הרשמה של Alpaca: לא אומתו. Schwab מבוסס על מקורות משניים בלבד. |
| `parts/A3.md` — ללא מפתח (ארה"ב) | קיים | 24,696 בייט | Stooq ❌; CNBC `restQuote` ❌. ToS של Finviz (404) ו־CNBC (403) לא נקראו. A3 כתב ש־`range=max` נותן יומי מ־1984 — העורך תיקן ל־3mo. TradingView CORS לא נבדק בדפדפן אמיתי. |
| `parts/A4.md` — TASE/אירופה/אסיה/אופציות | קיים | 31,330 בייט | TASE DataHub סגור, המוצרים החינמיים לא אומתו מול TASE. `api.tase.co.il` תוך־יומי לא נבדק (בורסה סגורה). Börse Frankfurt אי־התאמה מול Yahoo לא הוכרעה. JPX לא נבדק. Euronext/Investing/Maya ❌. ToS של Börse Frankfurt ו־optionsDX לא נקראו. |
| `parts/B1.md` — חשבונות דמו | קיים | 47,960 בייט | IBKR KB, Schwab paperMoney, Investopedia FAQ, MarketWatch חסומים (403/401) → טענות (לא אומת). מספר טענות "מזיכרון" ללא קישור (סומנו). Webull: מודל מילוי/שורט/מרג'ין לא נמצאו. TradeStation: כמעט הכול לא נמצא. מצב MarketWatch VSE ב־2026 לא ידוע. |
| `parts/B2.md` — קוד פתוח + נוסחאות | קיים | 52,083 בייט | Lean BrokerageModel ומינוף 2x לא אומתו בקוד. backtrader ו־Nautilus נבדקו חלקית דרך סוכן משנה. Y בחוק השורש, ספרד טיפוסי במניות קטנות, עמלות HTB: לא אומתו. SSRN 403. אין מימוש קוד ל־Amihud/Kyle. |

DONE
