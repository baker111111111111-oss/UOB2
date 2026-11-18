from flask import Flask, render_template_string, request, redirect, url_for, session, send_file
from datetime import datetime
import os
import base64
import sqlite3
import io
import csv

app = Flask(__name__)
app.secret_key = 'cofarts_absolute_infinity_masterpiece_2026'

HEAD_OFFICIAL_EMAIL = "head.dept@cofarts.uobaghdad.edu.iq"
DB_NAME = 'cofarts_system.db'

def init_db():
    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS reports (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            doctor TEXT,
            dept TEXT,
            shift TEXT,
            stage TEXT,
            section TEXT,
            subject TEXT,
            timestamp TEXT,
            present_count INTEGER,
            absent_count INTEGER,
            details TEXT
        )
    ''')
    conn.commit()
    conn.close()

init_db()

COLLEGE_DEPARTMENTS = [
    {"name": "قسم التربية الفنية", "icon": "🎨", "desc": "منارة الفن التعبيري وجماليات التربية الحية"},
    {"name": "قسم الفنون الموسيقية", "icon": "🎵", "desc": "أصالة النغم، الإيقاع، والتذوق الصوتي الرفيع"},
    {"name": "قسم الخط العربي والزخرفة", "icon": "✒️", "desc": "هندسة الحرف العربي وإشراقات الزخرفة الإسلامية"},
    {"name": "قسم الفنون التشكيلية", "icon": "🖼️", "desc": "عراقة الرسم، النحت، والتشكيل الجداري المعاصر"},
    {"name": "قسم الفنون المسرحية", "icon": "🎭", "desc": "خشبة الإبداع، التمثيل، والإخراج المسرحي الرقمي"},
    {"name": "قسم الفنون السينمائية والتلفزيونية", "icon": "🎬", "desc": "السينما التفاعلية، الإخراج التلفزيوني والوسائط"},
    {"name": "قسم التصميم", "icon": "✏️", "desc": "هندسة الفضاء البصري، التصميم الداخلي والصناعي"}
]

FULL_COLLEGE_DATA = {
    "قسم التربية الفنية": {
        'صباحي': {
            'المرحلة الرابعة': {
                'الشعبة (أ)': [
                    "استبرق رستم حمادي فياض", "أثمار باسم محمد كيطان", "إيلاف خالد عبد الرزاق احمد",
                    "بنين هاشم محمد جبر", "ايناس جلال سعدون محمد", "حسين حسن رحيم عبد",
                    "زهراء عصام احمد اسماعيل", "زهراء علي جلوب جودة", "زهراء كريم كاظم راضي",
                    "ساره احمد نظام الدين محمد امين", "سجاد حسين هادي يوسف", "صفوت رائد جاسم محمد",
                    "ضحى اسعد حسن عطية", "عبد المعز عماد شمسي خضير", "علا علاء سلمان حاوي",
                    "علاء الدين محمد علي صالح", "عيسى عبدالله بلادي محمد", "فاطمه صبيح حسن جباره",
                    "فضيلة عامر محسن جاسم", "مروه عماد خلف كردي", "نرجس جليل محسن عبد الحسين",
                    "نور أسامة محمد علي", "احمد كريم مريوش لازم", "عبدالله خالد نعمة", "عبدالله ستار كركان مبدر"
                ],
                'الشعبة (ب)': [
                    "بنين قاسم حسن وادي", "تاره وسام راضي جاسم", "تبارك حسين علاء الدين شهاب",
                    "جنات أركان أحمد عبد الله", "رحمة محمد فاضل عبد الله", "رؤى حسين علي عبد",
                    "ريام أياد موفق عبد الله", "زهراء رياض عبد الرضا عباس", "زهراء عماد حمادي حيدر",
                    "زينب رعد مايع جاسم", "زينب فرج سالم ثابت", "زينب كاظم محمد حنتوش",
                    "سجاد سمير صابر حسان", "شمس محمد عبد الجبار رجب", "علي محمد حسن عطية",
                    "غفران حامد حسين دكش", "فهد وعد عباس درويش", "قمر خليل إبراهيم صالح",
                    "لينا ضياء جاسم داود", "موسى كاظم حمزة كاظم", "محمد قاسم موجد داغر",
                    "مهدي صلاح مهدي علي", "سلوان سمير حسين"
                ],
                'الشعبة (ج)': [
                    "ايناس باسم محمد نايف", "ايمان رياض صلال عبد", "ايه حسين صدام كاطع",
                    "إسراء عبد الكريم عبد المجيد سلمان", "بيداء حسن علي عباس", "زينب صادق عبد الكريم علوان",
                    "شهد وعد حميد هراطة", "غسق سلام عبد خلف", "فاطمة علي هادي عباس",
                    "فهد تحسين خضير عباس", "كوثر علي حسين جليل", "مصطفى رافد صبري مكي",
                    "منتظر عصام جبر عبد الساده", "نبأ عامر خالد عداي", "نور اليقين علي حسن جري",
                    "وسن قصي عباس تبينه", "مختار اسعد نجم عبد الحسن", "ياسر خليف إسماعيل عودة",
                    "عبد الخالق عماد عبود علي"
                ],
                'الشعبة (د)': [
                    "الاء نجم عبدالله مهدي", "بتول علي عبدالحسين خشان", "بكر سعيد حميد احمد",
                    "تقوى عبدالحسين سعودي", "حسين جبار حسن حواس", "ديار احمد جواد حسن",
                    "رانيا جودت عبد الكاظم عطيه", "رسل حارث حسن عبد الزهرة", "رقية محمد فهد عزيز",
                    "زهراء حميد خليف غراوي", "زينب محمد فهد عزيز", "عبد المهيمن بكر محمد جاسم",
                    "مروة محمد فاضل عباس", "مصطفى محمد مهدي صالح", "محمد حسن محمد حسن", "زمن حسين علي"
                ]
            },
            'المرحلة الثالثة': {
                'الشعبة (أ)': [
                    "ابو الحسن علي جبار محمد", "اية رائد علي عبد الكريم", "غدير تحسين كاطع هليل",
                    "ثريا صادق عطية هادي", "حنين عدنان حسين كاظم", "رند خليل قحطان خليل",
                    "رؤى حسن حميد كريم", "زهراء احمد عبد الحسين مجيد", "زينب دريد فالح مطشر",
                    "عباس طالب محسن ظاهر", "عبد الرحمن عبد الفتاح مصطفى", "عبد الله حكمت سعدي مجيد",
                    "عبد الملك حسام عبد الملك صبري", "علي جمال محمد حسين", "علي حسن شريف عبد",
                    "علي رفعت كاظم عبد الله", "علي صاحب هادي عبد الله", "فرح فاضل طالب حسن",
                    "قسمة تكليف شكر محمود", "محمد ابراهيم جواد كاظم", "ملاك موعود محمد نجم",
                    "نمارق صلاح فتاح مبارك", "هبة وليد خالد جاسم", "ياسر عمار عماد برغش", "عبدالله خالد صبري محمد"
                ],
                'الشعبة (ب)': [
                    "احمد اياد طة حمد", "آمنة عمر قيس حمزة", "ايلاف احمد شوشي حسن",
                    "بنين نجم عبد الاله كرم", "حنين هيثم لطيف مهدي", "روان احمد صالح مصطفى",
                    "رؤيا حسن علي حسين", "زهراء حارث غازي جار الله", "زهراء سعد مهدي عبيد",
                    "شهناز عباس كريم محمد", "العباس عدي حسين عزيز", "عبد الله عماد شمسي خضير",
                    "علا ابراهيم ياسين ظاهر", "علي جاسم محمد علي", "فاطمة علاء عبد عبود",
                    "فرح تحسين كاظم حياوي", "مريم فالح حسين لفتة", "مريم حسين هاشم حسين",
                    "مصطفى عدنان هاشم سلمان", "ميار زياد طارق عبد اللطيف", "نور كمال فارس عبد الرزاق",
                    "يوسف احمد نعيم عبد الله", "يوسف هشام ثامر سرحان"
                ],
                'الشعبة (ج)': [
                    "احمد حيدر فاروق رفيق", "تبارك عبد الله فاخر قاسم", "جمانة محمد عبد الجبار محمود",
                    "جيهان جمال حطاب وشر", "حوراء سمير عبد احمد", "حوراء عدنان اغا مهدي",
                    "دانية باسم بيد الله زغير", "زهراء احمد فاروق احمد", "زهراء ماهر موسى شاكر",
                    "زينب علي عبد الحسين ثامر", "سفانه امين خضير عبود", "عباس مهدي عبد الأمير مشعان",
                    "علي رافع علي عبد", "علي عبد الله حسين عباس", "فاطمة عقيل ابراهيم حسن",
                    "مريم محمد خضير سلمان", "مها ابراهيم جبار جاسم", "ملاك حيدر عزيز عبد الله"
                ],
                'الشعبة (د)': [
                    "احمد حسين عبد العظيم كاظم", "اسراء رزاق مولي عبد", "امنة باسم محمد جاسم",
                    "دانية عمر فاروق عبد الله", "دعاء رعد جودة بهير", "ربى مهند علي صالح",
                    "رقية خالد جسام نصيف", "روعة كريم شيحان جواد", "زهراء ابراهيم محمد صاحب",
                    "زهراء سعد خليل جاسم", "زهراء عباس عبد الامير مبارك", "سارة خالد حلو صكر",
                    "عبدالله كمال محمود جميل", "علي هادي خليف كريم", "فرح احمد عجمي هويدي",
                    "كواكب سعدي حمادي مدب", "مريم محمود حسين محمد", "مسار محمد كامل سالم",
                    "مقداد كاظم محسن كرم", "منه عبد الله سالم مكي", "مروة محمد فاضل"
                ]
            },
            'المرحلة الثانية': {
                'الشعبة (أ)': [
                    "جميلة رعد جمال فهمي", "در ذو الفقار علي حسي", "زهراء ابراهيم عزيز محيسن",
                    "سارة عمار صباح عبد الجبار", "سجاد ياسر علي محمد", "طيبة ياسر خضير زيدان",
                    "عبد الله احمد فياض", "غدير انور صباح عبد الامير", "غدير عقيل كاظم",
                    "فاطمة ناهض عبد علي عبد الخضر", "محمد المهدي محمد عبد الرضا حسين",
                    "مصطفى احمد سعيد صالح", "نرجس حسن هاشم مزيد", "نرجس قحطان مرجان عيسى",
                    "نور عادل امجيد عطوي", "نور عدنان مصطفى", "نور محمد فخري جاسم"
                ],
                'الشعبة (ب)': [
                    "احمد عبد الكاظم سعد وادي", "افنان اسعد قاسم اجخيور", "ايه سعد خلف فليح",
                    "براء باسم كاظم كاطع", "تقى مثنى محمد علي", "رسل طالب كريم ثامر",
                    "زهراء عمار علك قاسم", "زهراء فلاح بلاسم شخير", "سجى قاهر صدام",
                    "طه ستار دلي كاظم", "عبد الله امجد ارشد مهدي", "علي حميد ناصر علاوي",
                    "فاطمة صالح جحيل خلف", "مسلم كاظم عبود حسن", "مينا سعد حسن صالح",
                    "نادين هادي فهد حرج", "حيدر ماجد ياسين"
                ],
                'الشعبة (ج)': [
                    "علي عبدالله حسين", "رانية عماد عسكر", "رقية علي كريم حسن",
                    "زهراء حفظي عبيد سلوم", "زينب ميثم مجيد حميد", "سجى فاضل خليل حسن",
                    "ضحى عباس دهش دهيمان", "عبد الله حسين عليوي نخيل", "فاطمة علي فنجان علي",
                    "كرار محمد علي سيد", "محمد قيس لازم حاتم", "مودة عامر عبد الخضر جاسم",
                    "ميثم ناقل كاظم نعمة", "نبأ عماد الدين عبد الامير بنيان", "ندى علي عبد عبد الله",
                    "نرجس احمد قاسم رحيم", "نرجس قصي خضير احمد", "نور علي جاسم بنوان",
                    "نور وصفي مشعان فاضل", "ياسين حيدر فاضل ابراهيم"
                ],
                'الشعبة (د)': [
                    "انور فارس كريم وادي", "تبارك احمد عبد الكاظم", "حمزة ساير عايد رعيد",
                    "رحمة وليد حسن شهباز", "زهراء جواد كاظم عياش", "الزهراء علي احمد جاسم",
                    "زهراء غانم عبدالعباس عيسى", "زينب علي داود", "زينب علي محمد سيد",
                    "شيماء حسن جاسم ابراهيم", "طيبة محمود شاكر", "عبد الله ابراهيم عبد اللطيف عبد المجيد",
                    "عبد الله باسم سليم طعمة", "علي حسين علوان", "علي ساير عايد رعيد",
                    "علي قحطان عدنان جاسم", "كرار محمد دهام", "مريم محسن علي فرحان",
                    "مريم مهند حسين", "مصطفى باسم محمد", "هارون اياد محمد عليوي", "وردة عامر محسن علي"
                ]
            }
        },
        'مسائي': {
            'المرحلة الرابعة': {
                'الشعبة (أ)': [
                    "اسراء حمادي ضيدان وهب", "حسين سلمان نجم عبود", "شيلان طالب سلمان امام علي",
                    "غيث كريم تامول سعد", "زينه محمد حسين سالم", "حمزه جعفر موسى عبد",
                    "علياء يعقوب علي غلام", "بتول فالح سالم ثجيل", "سهاد اسماعيل خليل عيدان",
                    "رقية عادل خشن كريم", "محمد ثائر مولود شاكر", "احمد قاسم سلوم"
                ],
                'الشعبة (ب)': [
                    "علياء عبد الرضا سلطان شكر", "سيف حسين عبد علي", "نبراس علي برهان حسين",
                    "علي سهيل نجم عبدالله", "محمد كريم عبد عيد", "شيماء شاكر مجلوف كزار",
                    "علي عباس عبد الامير", "محمد حسن عبدالله احمد", "طارق عبد الزهره حمدان فياض", "حسين علي حسين"
                ]
            },
            'المرحلة الثالثة': {
                'الشعبة (أ)': [
                    "اسماء علاء عادل عبد الرزاق", "امل جواد كاظم متعب", "دعاء علي احمد حمود",
                    "ريام سمير عبد الامير جاسم", "زينب عبد الرزاق ماضي رسولي", "سمار صادق براك حاجم",
                    "عبد الله حسين عليوي محمد", "محمد عباس برهي خليل", "مروة خالد جراد حمد",
                    "نغم طالب خضير ظاهر", "وليد خالد مطر مرموط", "كرار حيدر قاسم محمد", "محمد قاسم سلوم"
                ],
                'الشعبة (ب)': [
                    "ابراهيم فالح حسين", "اشرف محمد حسن كاظم", "دعاء احمد جاسم احمد",
                    "علي عباس طاهر", "عمار موسى جعفر طة", "غفران كريم منصور عبد الله",
                    "فاطمة هاشم جبار علي", "كاظم عطية كاظم غالي", "مصطفي علكم زروك",
                    "نضال جوني خضير عنبر", "نور خير الله جاسم عبد", "هيا فيصل هاشم"
                ]
            },
            'المرحلة الثانية': {
                'الشعبة (أ)': [
                    "اسماء حربي جميل", "اسيل اياد حسين", "سالي سرهد صفر", "سجاد كاظم زيارة",
                    "طيبة عبد الحسن خزعل", "علي عادل زغير", "نور جبار حربي", "نور ستار جبار",
                    "هبة نبيل ابراهيم", "هدى كاظم رشيد", "هناء حسين كاطع"
                ],
                'الشعبة (ب)': [
                    "اثمار صباح خلف", "نوره حسن محمد", "رضا مصطفى محمود", "زهراء عباس مطرود",
                    "سرى قاسم محمد", "كاظمية محمد نجم", "لقاء عبد السلام أحسين", "ميساء محمد يوسف",
                    "نور علي عبد الحسين", "نورس احمد حسن", "زهراء محمد رضا حسن", "تبارك محمد جمعة محمد"
                ]
            }
        }
    }
}

for dept_item in COLLEGE_DEPARTMENTS[1:]:
    d_name = dept_item["name"]
    FULL_COLLEGE_DATA[d_name] = {
        'صباحي': {
            'المرحلة الأولى': {'الشعبة (أ)': ["طالب نموذجي 1", "طالب نموذجي 2"]}
        },
        'مسائي': {
            'المرحلة الأولى': {'الشعبة (أ)': ["طالب مسائي 1", "طالب مسائي 2"]}
        }
    }

def get_logo_base64():
    for path in [os.path.expanduser('~/logo.png'), 'logo.png']:
        if os.path.exists(path):
            with open(path, 'rb') as img_file:
                return "data:image/png;base64," + base64.b64encode(img_file.read()).decode('utf-8')
    return ""

BASE_CSS_AND_HEAD = """
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#050a18">
    <title>جامعة بغداد - كلية الفنون الجميلة</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800;900&display=swap');
        
        :root {
            --bg-deep: #000207;
            --glass-bg: rgba(5, 10, 24, 0.96);
            --glass-border: rgba(245, 158, 11, 0.65);
            --accent-gold: #f59e0b;
            --accent-gold-glow: rgba(245, 158, 11, 0.85);
            --accent-green: #10b981;
            --accent-blue: #3b82f6;
            --text-light: #f8fafc;
            --text-muted: #94a3b8;
        }

        * { box-sizing: border-box; }

        body {
            font-family: 'Cairo', sans-serif;
            background-color: var(--bg-deep);
            color: var(--text-light);
            margin: 0;
            padding: 15px;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow-x: hidden;
        }

        #particles-canvas {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            z-index: 0; pointer-events: none;
        }

        .main-container {
            position: relative;
            z-index: 2;
            width: 100%;
            max-width: 1200px;
            background: var(--glass-bg);
            border-radius: 32px;
            padding: 30px;
            box-shadow: 0 40px 100px rgba(0,0,0,0.95), 0 0 60px rgba(245, 158, 11, 0.35);
            border: 2px solid var(--glass-border);
            backdrop-filter: blur(25px);
        }

        .brand-header {
            text-align: center;
            border-bottom: 2px solid rgba(245, 158, 11, 0.4);
            padding-bottom: 20px;
            margin-bottom: 25px;
        }

        .logo-img {
            width: 110px; height: 110px;
            object-fit: contain; margin-bottom: 10px;
            border-radius: 50%; border: 3px solid var(--accent-gold);
            background: #000207; padding: 6px;
            box-shadow: 0 0 45px var(--accent-gold-glow);
        }

        .brand-header h1 {
            margin: 0; font-size: 2.3rem; font-weight: 900;
            background: linear-gradient(135deg, #fbbf24 0%, #f59e0b 50%, #d97706 100%);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
        }

        .brand-header h2 {
            margin: 5px 0 0 0; font-size: 1.4rem; font-weight: 700;
            color: var(--accent-green);
        }

        .nav-bar {
            display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px;
            background: rgba(14, 24, 46, 0.94); padding: 15px 25px;
            border-radius: 20px; margin-bottom: 25px;
            border: 1px solid rgba(255, 255, 255, 0.15);
        }

        .logout-link {
            color: #ef4444; text-decoration: none; font-weight: 800;
            padding: 8px 18px; border: 1.5px solid #ef4444; border-radius: 12px;
        }

        .dept-grid {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 18px; margin-bottom: 25px;
        }

        .dept-card {
            background: rgba(14, 24, 46, 0.9); border: 1.5px solid rgba(255, 255, 255, 0.15);
            padding: 22px; border-radius: 22px; cursor: pointer; text-decoration: none; color: inherit;
            display: block; transition: all 0.3s;
        }

        .dept-card:hover {
            border-color: var(--accent-gold); transform: translateY(-5px);
            box-shadow: 0 15px 35px rgba(245, 158, 11, 0.4);
        }

        .btn-action {
            background: linear-gradient(135deg, var(--accent-gold), #b45309);
            color: #fff; font-weight: 900; padding: 16px 28px;
            border: none; border-radius: 16px; cursor: pointer;
            width: 100%; font-size: 1.2rem; font-family: 'Cairo', sans-serif;
            box-shadow: 0 10px 30px rgba(245, 158, 11, 0.5);
        }

        .input-field {
            width: 100%; padding: 14px 18px; border-radius: 14px;
            border: 1.5px solid rgba(255, 255, 255, 0.2);
            background: rgba(6, 11, 25, 0.95); color: #fff;
            font-size: 1.05rem; font-family: 'Cairo', sans-serif; margin-bottom: 18px;
        }

        .portal-switcher {
            display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 25px;
        }

        .role-card-select {
            background: rgba(10, 18, 38, 0.75); border: 2px solid rgba(255, 255, 255, 0.12);
            padding: 18px; border-radius: 18px; cursor: pointer; text-align: center;
        }

        .role-card-select.active-doctor { border-color: var(--accent-gold); background: rgba(245, 158, 11, 0.2); }
        .role-card-select.active-head { border-color: var(--accent-green); background: rgba(16, 185, 129, 0.2); }

        table { width: 100%; border-collapse: collapse; margin-top: 15px; background: rgba(6, 11, 25, 0.95); border-radius: 14px; overflow: hidden; }
        th, td { border-bottom: 1px solid rgba(255, 255, 255, 0.1); padding: 12px; text-align: right; font-size: 0.95rem; }
        th { background: rgba(14, 24, 46, 0.99); color: var(--accent-gold); }

        .pill-btn {
            display: inline-block; padding: 10px 22px; margin: 6px;
            border-radius: 30px; border: 1.5px solid var(--accent-blue);
            color: var(--accent-blue); text-decoration: none; font-weight: 700;
        }
        .pill-btn:hover, .pill-btn.active { background: var(--accent-blue); color: #fff; }
        
        .support-footer {
            text-align: center; margin-top: 25px; padding-top: 15px;
            border-top: 1px solid rgba(255,255,255,0.1); font-size: 0.9rem; color: var(--text-muted);
        }
        .support-footer a { color: var(--accent-gold); text-decoration: none; font-weight: bold; }
    </style>
</head>
<body>
<canvas id="particles-canvas"></canvas>
<div class="main-container">
    <div class="brand-header">
        {% if logo_b64 %}<img src="{{ logo_b64 }}" class="logo-img">{% endif %}
        <h1>🏛 جامعة بغداد</h1>
        <h2>كلية الفنون الجميلة</h2>
    </div>
"""

FOOTER_SCRIPTS = """
    <div class="support-footer">
        الدعم الفني والتطوير: <a href="https://t.me/beko141" target="_blank">تواصل عبر تيليجرام (@beko141)</a>
    </div>
</div>
<script>
    const canvas = document.getElementById('particles-canvas');
    const ctx = canvas.getContext('2d');
    let particles = [];
    function resize() { canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
    window.addEventListener('resize', resize); resize();
    for (let i = 0; i < 45; i++) {
        particles.push({
            x: Math.random() * canvas.width, y: Math.random() * canvas.height,
            r: Math.random() * 2.5 + 1, dx: (Math.random() - 0.5) * 1, dy: (Math.random() - 0.5) * 1
        });
    }
    function animate() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = 'rgba(245, 158, 11, 0.5)';
        particles.forEach(p => {
            p.x += p.dx; p.y += p.dy;
            if (p.x < 0 || p.x > canvas.width) p.dx *= -1;
            if (p.y < 0 || p.y > canvas.height) p.dy *= -1;
            ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2); ctx.fill();
        });
        requestAnimationFrame(animate);
    }
    animate();
</script>
</body>
</html>
"""

@app.route('/')
def index():
    if 'user_role' in session:
        if session['user_role'] == 'head':
            return redirect(url_for('head_dashboard'))
        return redirect(url_for('departments'))
    
    error = request.args.get('error')
    html = BASE_CSS_AND_HEAD + """
    <div style="max-width: 550px; margin: 0 auto;">
        {% if error %}<div style="background:rgba(239,68,68,0.2); border-right:5px solid #ef4444; padding:12px; border-radius:10px; margin-bottom:15px; color:#fca5a5;">⚠️ {{ error }}</div>{% endif %}
        
        <div style="background: rgba(245,158,11,0.15); border-right: 5px solid var(--accent-gold); padding: 15px; border-radius: 14px; margin-bottom: 20px; font-size: 0.95rem; line-height: 1.7;">
            ✨ <strong>البوابة السيادية الموحدة للتحقق الأكاديمي:</strong><br>
            أهلاً ومرحباً بسفير الفن والعلم في رحاب كلية الفنون الجميلة. اختر صفتك الأكاديمية للمتابعة.
        </div>

        <div class="portal-switcher">
            <div class="role-card-select active-doctor" id="card-doctor" onclick="setRole('doctor')">
                <div style="font-size: 2rem;">👨‍🏫</div>
                <div style="font-weight: 800; margin-top:5px;">أستاذ المادة</div>
            </div>
            <div class="role-card-select" id="card-head" onclick="setRole('head')">
                <div style="font-size: 2rem;">🏛️</div>
                <div style="font-weight: 800; margin-top:5px;">مقرر القسم</div>
            </div>
        </div>

        <form action="/login" method="POST">
            <input type="hidden" name="role_type" id="selected_role" value="doctor">
            <label style="color:var(--accent-gold); font-weight:bold; display:block; margin-bottom:6px;">الاسم الكامل / اللقب العلمي:</label>
            <input type="text" name="user_name" class="input-field" placeholder="مثال: د. أحمد علي" required>
            
            <label style="color:var(--accent-gold); font-weight:bold; display:block; margin-bottom:6px;">البريد الإلكتروني الجامعي أو الشخصي:</label>
            <input type="email" name="user_email" id="user_email" class="input-field" placeholder="name@cofarts.uobaghdad.edu.iq" required>
            
            <button type="submit" class="btn-action">دخول المنظومة الأكاديمية</button>
        </form>
    </div>
    <script>
        function setRole(role) {
            document.getElementById('selected_role').value = role;
            document.getElementById('card-doctor').className = 'role-card-select' + (role === 'doctor' ? ' active-doctor' : '');
            document.getElementById('card-head').className = 'role-card-select' + (role === 'head' ? ' active-head' : '');
            if(role === 'head') {
                document.getElementById('user_email').placeholder = '""" + HEAD_OFFICIAL_EMAIL + """';
            } else {
                document.getElementById('user_email').placeholder = 'name@cofarts.uobaghdad.edu.iq';
            }
        }
    </script>
    """ + FOOTER_SCRIPTS
    return render_template_string(html, logo_b64=get_logo_base64(), error=error)

@app.route('/login', methods=['POST'])
def login():
    role = request.form.get('role_type')
    name = request.form.get('user_name')
    email = request.form.get('user_email').strip().lower()
    
    session['user_role'] = role
    session['user_name'] = name
    session['user_email'] = email
    
    if role == 'head':
        return redirect(url_for('head_dashboard'))
    return redirect(url_for('departments'))

@app.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('index'))

@app.route('/departments')
def departments():
    if 'user_role' not in session:
        return redirect(url_for('index'))
    
    html = BASE_CSS_AND_HEAD + """
    <div class="nav-bar">
        <div>مرحباً بك: <strong style="color:var(--accent-gold);">{{ user_name }}</strong></div>
        <a href="/logout" class="logout-link">تسجيل الخروج</a>
    </div>
    <h3 style="text-align:center; color:var(--accent-gold); margin-bottom:20px;">اختر القسم الأكاديمي</h3>
    <div class="dept-grid">
        {% for d in depts %}
        <a href="/select_options?dept={{ d.name }}" class="dept-card">
            <div style="font-size:2.5rem; margin-bottom:10px;">{{ d.icon }}</div>
            <div style="font-size:1.25rem; font-weight:800; color:var(--accent-gold);">{{ d.name }}</div>
            <div style="font-size:0.9rem; color:var(--text-muted); margin-top:6px;">{{ d.desc }}</div>
        </a>
        {% endfor %}
    </div>
    """ + FOOTER_SCRIPTS
    return render_template_string(html, logo_b64=get_logo_base64(), user_name=session['user_name'], depts=COLLEGE_DEPARTMENTS)

@app.route('/select_options')
def select_options():
    if 'user_role' not in session:
        return redirect(url_for('index'))
    dept = request.args.get('dept', 'قسم التربية الفنية')
    shift = request.args.get('shift', 'صباحي')
    
    dept_data = FULL_COLLEGE_DATA.get(dept, {})
    shifts = list(dept_data.keys())
    stages = list(dept_data.get(shift, {}).keys())
    
    html = BASE_CSS_AND_HEAD + """
    <div class="nav-bar">
        <div>القسم: <strong style="color:var(--accent-gold);">{{ dept }}</strong></div>
        <div>
            <a href="/departments" style="color:var(--accent-green); text-decoration:none; margin-left:15px; font-weight:bold;">← الأقسام</a>
            <a href="/logout" class="logout-link">خروج</a>
        </div>
    </div>

    <div style="background:rgba(14,24,46,0.9); padding:25px; border-radius:20px; border:1px solid rgba(255,255,255,0.15);">
        <h3 style="color:var(--accent-gold); margin-top:0;">1. اختر نوع الدراسة:</h3>
        <div style="margin-bottom:25px;">
            {% for s in shifts %}
            <a href="/select_options?dept={{ dept }}&shift={{ s }}" class="pill-btn {% if s == shift %}active{% endif %}">الدراسة {{ s }}ة</a>
            {% endfor %}
        </div>

        <h3 style="color:var(--accent-gold);">2. اختر المرحلة والشعبة لرصد الحضور:</h3>
        {% for st in stages %}
            <div style="margin-bottom:15px; padding:15px; background:rgba(5,10,24,0.8); border-radius:14px;">
                <strong style="color:var(--accent-green); display:block; margin-bottom:10px;">{{ st }}:</strong>
                {% for sec in dept_data[shift][st].keys() %}
                    <a href="/attendance?dept={{ dept }}&shift={{ shift }}&stage={{ st }}&section={{ sec }}" class="pill-btn">{{ sec }}</a>
                {% endfor %}
            </div>
        {% endfor %}
    </div>
    """ + FOOTER_SCRIPTS
    return render_template_string(html, logo_b64=get_logo_base64(), dept=dept, shift=shift, shifts=shifts, stages=stages, dept_data=dept_data)

@app.route('/attendance', methods=['GET', 'POST'])
def attendance():
    if 'user_role' not in session:
        return redirect(url_for('index'))
        
    dept = request.args.get('dept')
    shift = request.args.get('shift')
    stage = request.args.get('stage')
    section = request.args.get('section')
    
    students = FULL_COLLEGE_DATA.get(dept, {}).get(shift, {}).get(stage, {}).get(section, [])
    
    if request.method == 'POST':
        subject = request.form.get('subject', 'مادة دراسية')
        present = 0
        absent = 0
        details_list = []
        
        for idx, st_name in enumerate(students):
            status = request.form.get(f'status_{idx}', 'حاضر')
            if status == 'حاضر':
                present += 1
            else:
                absent += 1
                details_list.append(f"{st_name} ({status})")
                
        conn = sqlite3.connect(DB_NAME)
        cursor = conn.cursor()
        cursor.execute('''
            INSERT INTO reports (doctor, dept, shift, stage, section, subject, timestamp, present_count, absent_count, details)
            VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
        ''', (session['user_name'], dept, shift, stage, section, subject, datetime.now().strftime("%Y-%m-%d %H:%M"), present, absent, " | ".join(details_list) if details_list else "لا يوجد غياب"))
        conn.commit()
        conn.close()
        
        return render_template_string(BASE_CSS_AND_HEAD + """
        <div style="text-align:center; padding:30px;">
            <div style="font-size:4rem;">✅</div>
            <h2 style="color:var(--accent-green);">تم إرسال وحفظ سجل الحضور بنجاح!</h2>
            <p style="color:var(--text-muted);">الحضور: {{ present }} | الغياب/الإجازات: {{ absent }}</p>
            <br>
            <a href="/departments" class="btn-action" style="text-decoration:none; display:inline-block; width:auto;">العودة للأقسام</a>
        </div>
        """ + FOOTER_SCRIPTS, logo_b64=get_logo_base64(), present=present, absent=absent)

    html = BASE_CSS_AND_HEAD + """
    <div class="nav-bar">
        <div><strong>{{ dept }}</strong> - {{ shift }} - {{ stage }} ({{ section }})</div>
        <a href="/select_options?dept={{ dept }}&shift={{ shift }}" style="color:var(--accent-gold); text-decoration:none; font-weight:bold;">← رجوع</a>
    </div>

    <form method="POST">
        <label style="color:var(--accent-gold); font-weight:bold; display:block; margin-bottom:6px;">اسم المادة الدراسية:</label>
        <input type="text" name="subject" class="input-field" placeholder="اكتب اسم المادة هنا..." required>

        <table>
            <thead>
                <tr>
                    <th style="width:40px;">ت</th>
                    <th>اسم الطالب</th>
                    <th style="width:120px;">الحالة</th>
                </tr>
            </thead>
            <tbody>
                {% for st in students %}
                <tr>
                    <td>{{ loop.index }}</td>
                    <td style="font-weight:bold;">{{ st }}</td>
                    <td>
                        <select name="status_{{ loop.index0 }}" style="padding:8px; border-radius:8px; background:#050a18; color:#fff; border:1px solid #334155; font-family:'Cairo'; width:100%;">
                            <option value="حاضر">حاضر ✅</option>
                            <option value="غائب">غائب ❌</option>
                            <option value="إجازة">إجازة 📝</option>
                        </select>
                    </td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
        <br>
        <button type="submit" class="btn-action">اعتماد وإرسال التقرير لمقرر القسم</button>
    </form>
    """ + FOOTER_SCRIPTS
    return render_template_string(html, logo_b64=get_logo_base64(), dept=dept, shift=shift, stage=stage, section=section, students=students)

@app.route('/head_dashboard')
def head_dashboard():
    if session.get('user_role') != 'head':
        return redirect(url_for('index'))
        
    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM reports ORDER BY id DESC')
    reports = cursor.fetchall()
    conn.close()
    
    html = BASE_CSS_AND_HEAD + """
    <div class="nav-bar">
        <div>لوحة مقرر القسم: <strong style="color:var(--accent-green);">{{ user_name }}</strong></div>
        <div>
            <a href="/export_csv" style="background:var(--accent-green); color:#fff; padding:8px 16px; border-radius:10px; text-decoration:none; font-weight:bold; margin-left:10px;">📥 تصدير Excel/CSV</a>
            <a href="/logout" class="logout-link">خروج</a>
        </div>
    </div>
    
    <h3 style="color:var(--accent-gold);">سجلات الحضور والغياب المستلمة ({{ reports|length }}):</h3>
    <div style="overflow-x:auto;">
        <table>
            <thead>
                <tr>
                    <th>التاريخ</th>
                    <th>الأستاذ</th>
                    <th>القسم والمرحلة</th>
                    <th>المادة</th>
                    <th>الحضور / الغياب</th>
                    <th>الطلبة الغائبون</th>
                </tr>
            </thead>
            <tbody>
                {% for r in reports %}
                <tr>
                    <td>{{ r[7] }}</td>
                    <td>{{ r[1] }}</td>
                    <td>{{ r[2] }} ({{ r[3] }} - {{ r[4] }} {{ r[5] }})</td>
                    <td>{{ r[6] }}</td>
                    <td><span style="color:#10b981;">{{ r[8] }} حاضر</span> / <span style="color:#ef4444;">{{ r[9] }} غائب</span></td>
                    <td style="font-size:0.85rem; color:#fca5a5;">{{ r[10] }}</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>
    """ + FOOTER_SCRIPTS
    return render_template_string(html, logo_b64=get_logo_base64(), user_name=session['user_name'], reports=reports)

@app.route('/export_csv')
def export_csv():
    conn = sqlite3.connect(DB_NAME)
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM reports ORDER BY id DESC')
    rows = cursor.fetchall()
    conn.close()
    
    si = io.StringIO()
    si.write('\ufeff')
    cw = csv.writer(si)
    cw.writerow(['ID', 'الأستاذ', 'القسم', 'الدراسة', 'المرحلة', 'الشعبة', 'المادة', 'الوقت', 'عدد الحضور', 'عدد الغياب', 'تفاصيل الغياب'])
    cw.writerows(rows)
    
    output = io.BytesIO()
    output.write(si.getvalue().encode('utf-8'))
    output.seek(0)
    return send_file(output, mimetype="text/csv", as_attachment=True, download_name="cofarts_attendance_report.csv")

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port, debug=False)
