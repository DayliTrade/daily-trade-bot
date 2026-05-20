import os
import logging
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, ContextTypes

# إعداد السجلات
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(__name__)

# التوكن من متغيرات البيئة (سنضعه في Render لاحقاً)
BOT_TOKEN = os.environ.get('BOT_TOKEN')

# ✅ معرف المدير (تم وضع رقمك هنا)
ADMIN_ID = 6796143051

# بيانات الدفع (تم وضع محافظك الحقيقية هنا)
WALLETS = {
    "RedotPay": "1727023786",
    "BinancePay": "181957142"
}

# أمر البدء /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    if user_id != ADMIN_ID:
        await update.message.reply_text("🚫 الوصول مسموح فقط للمدير.")
        return

    keyboard = [
        [InlineKeyboardButton("🔑 تسجيل جديد / دخول", callback_data="login")],
        [InlineKeyboardButton("💳 دفع الاشتراك", callback_data="subscription")],
        [InlineKeyboardButton("📊 التداول اليدوي", callback_data="manual_trade")],
        [InlineKeyboardButton("🤖 التداول الآلي", callback_data="auto_trade")]
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    await update.message.reply_text(
        "👋 مرحباً مدير DailyTradeBot!\n✅ تم تفعيل صلاحية المدير.\nاختر من القائمة:",
        reply_markup=reply_markup
    )

# معالج الأزرار
async def button_click(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    data = query.data

    if data == "login":
        await query.edit_message_text(
            "✅ تم تسجيل الدخول بنجاح كـ Admin.\nالبوت جاهز للعمل!",
            reply_markup=InlineKeyboardMarkup([[InlineKeyboardButton("🔙 القائمة الرئيسية", callback_data="main_menu")]])        )

    elif data == "subscription":
        text = (
            " **خيارات الاشتراك:**\n\n"
            "1️⃣ RedotPay\n"
            f"المحفظة: `{WALLETS['RedotPay']}`\n\n"
            "2️⃣ BinancePay\n"
            f"المحفظة: `{WALLETS['BinancePay']}`\n\n"
            "📌 بعد الدفع، أرسل إيصال التحويل هنا لتفعيل الحساب."
        )
        await query.edit_message_text(
            text, parse_mode='Markdown',
            reply_markup=InlineKeyboardMarkup([[InlineKeyboardButton("🔙 رجوع", callback_data="main_menu")]])
        )

    elif data == "manual_trade":
        keyboard = [
            [InlineKeyboardButton("📈 أزواج OTC", callback_data="pair_otc")],
            [InlineKeyboardButton("🌍 أزواج حقيقية", callback_data="pair_real")],
            [InlineKeyboardButton("🔙 رجوع", callback_data="main_menu")]
        ]
        await query.edit_message_text(
            "📊 **التداول اليدوي**\nاختر نوع الزوج:",
            reply_markup=InlineKeyboardMarkup(keyboard)
        )

    elif data in ["pair_otc", "pair_real"]:
        pair_type = "OTC" if data == "pair_otc" else "حقيقية"
        await query.edit_message_text(
            f"📊 **تحليل الزوج ({pair_type})**\n\n"
            "🟢 الإشارة: شراء (BUY)\n"
            "💰 السعر: 1.0850\n"
            " القوة: 78%\n\n"
            "⚠️ التداول يحمل مخاطر. استخدم إدارة رأس المال.",
            reply_markup=InlineKeyboardMarkup([[InlineKeyboardButton("🔙 رجوع", callback_data="manual_trade")]])
        )

    elif data == "auto_trade":
        text = (
            "🤖 **إعدادات التداول الآلي**\n\n"
            "يرجى إدخال الإعدادات بالترتيب:\n"
            "1️⃣ نوع الأزواج (OTC/حقيقي)\n"
            "2️⃣ مبلغ الصفقة ($)\n"
            "3️⃣ وقف الخسارة ($)\n"
            "4️⃣ هدف الربح ($)\n\n"
            "بعد الإدخال، سيظهر زر 'بدء التداول'."
        )
        await query.edit_message_text(
            text,            reply_markup=InlineKeyboardMarkup([[InlineKeyboardButton("🔙 رجوع", callback_data="main_menu")]])
        )

    elif data == "main_menu":
        await start(update, context)

# تشغيل البوت
def main():
    if not BOT_TOKEN:
        logger.error("❌ لم يتم العثور على التوكن!")
        return
    
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(button_click))
    
    logger.info("🚀 البوت يعمل الآن...")
    app.run_polling(allowed_updates=Update.ALL_TYPES)

if __name__ == "__main__":
    main()
