[25.11.2026 18:36] Hasanboy Razzaqov: import asyncio
import logging
import os
from dotenv import load_dotenv

from aiogram import Bot, Dispatcher, F, types
from aiogram.filters import Command, CommandStart
from aiogram.fsm.context import FSMContext
from aiogram.fsm.state import State, StatesGroup
from aiogram.types import (
    BotCommand,
    InlineKeyboardButton,
    InlineKeyboardMarkup,
    KeyboardButton,
    ReplyKeyboardMarkup,
    ReplyKeyboardRemove,
)

load_dotenv()

BOT_TOKEN = os.getenv("BOT_TOKEN")
ADMIN_ID_RAW = os.getenv("ADMIN_ID")

if not BOT_TOKEN:
    raise ValueError("BOT_TOKEN topilmadi! Muhit o'zgaruvchilarini tekshiring.")

ADMIN_ID = int(ADMIN_ID_RAW) if ADMIN_ID_RAW and ADMIN_ID_RAW.isdigit() else None

logging.basicConfig(level=logging.INFO)

bot = Bot(token=BOT_TOKEN)
dp = Dispatcher()


class VolunteerForm(StatesGroup):
    full_name = State()
    age = State()
    phone = State()
    region = State()
    direction = State()
    confirm = State()


# --- MENYULAR ---
main_menu = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="📝 Volontyor bo‘lish")],
        [KeyboardButton(text="ℹ️ Loyiha haqida"), KeyboardButton(text="📞 Aloqa")],
    ],
    resize_keyboard=True,
)

cancel_menu = ReplyKeyboardMarkup(
    keyboard=[[KeyboardButton(text="❌ Bekor qilish")]],
    resize_keyboard=True,
)

phone_menu = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="📱 Telefon raqamni yuborish", request_contact=True)],
        [KeyboardButton(text="❌ Bekor qilish")],
    ],
    resize_keyboard=True,
)

direction_menu = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="🌱 Ekologiya"), KeyboardButton(text="📚 Ta'lim")],
        [KeyboardButton(text="🏥 Tibbiyot"), KeyboardButton(text="🤝 Ijtimoiy yordam")],
        [KeyboardButton(text="❌ Bekor qilish")],
    ],
    resize_keyboard=True,
)

confirm_inline = InlineKeyboardMarkup(
    inline_keyboard=[
        [
            InlineKeyboardButton(text="✅ Tasdiqlash", callback_data="confirm_yes"),
            InlineKeyboardButton(text="🔄 Qayta to‘ldirish", callback_data="confirm_no"),
        ]
    ]
)


async def set_commands(bot: Bot):
    commands = [
        BotCommand(command="start", description="Botni ishga tushirish"),
        BotCommand(command="register", description="Volontyor bo‘lish"),
        BotCommand(command="help", description="Yordam"),
    ]
    await bot.set_my_commands(commands)


# --- ASOSIY BUYRUQLAR ---
@dp.message(CommandStart())
async def cmd_start(message: types.Message, state: FSMContext):
    await state.clear()
    await message.answer(
        f"Assalomu alaykum, <b>{message.from_user.full_name}</b>!\n\n"
        "Volontyorlarni ro‘yxatga olish botiga xush kelibsiz.\n"
        "Ro‘yxatdan o‘tish uchun quyidagi menyudan foydalaning:",
        parse_mode="HTML",
        reply_markup=main_menu,
    )


@dp.message(Command("help"))
async def cmd_help(message: types.Message):
    await message.answer(
        "📌 <b>Botdan foydalanish yo‘riqnomasi:</b>\n"
        "• Ro‘yxatdan o‘tish uchun <b>«📝 Volontyor bo‘lish»</b> tugmasini bosing yoki /register buyrug‘ini yuboring.\n"
        "• Jarayonni to‘xtatish uchun istalgan payt <b>«❌ Bekor qilish»</b> tugmasini bosing.",
        parse_mode="HTML",
    )


@dp.message(F.text == "❌ Bekor qilish")
async def cancel_process(message: types.Message, state: FSMContext):
    await state.clear()
    await message.answer("Jarayon bekor qilindi.", reply_markup=main_menu)


@dp.message(F.text == "ℹ️ Loyiha haqida")
async def about_info(message: types.Message):
    await message.answer(
        "Bizning volontyorlik jamoamiz jamiyatga foydali tashabbuslarni birgalikda amalga oshiradi.\n"
        "Safimizga qo‘shilish uchun <b>«📝 Volontyor bo‘lish»</b> tugmasini bosing!",
        parse_mode="HTML",
    )


@dp.message(F.text == "📞 Aloqa")
async def contact_info(message: types.Message):
    await message.answer(
        "Savollar va takliflar uchun:\n"
        "Koordinator: @username\n"
        "Telefon: +998 90 123 45 67"
    )
[25.11.2026 18:36] Hasanboy Razzaqov: # --- RO'YXATDAN O'TISH BOSQICHLARI (5 TA SAVOL) ---
@dp.message(F.text == "📝 Volontyor bo‘lish")
@dp.message(Command("register"))
async def start_form(message: types.Message, state: FSMContext):
    await state.set_state(VolunteerForm.full_name)
    await message.answer(
        "<b>1/5.</b> Familiya, ism va sharifingizni kiriting:",
        parse_mode="HTML",
        reply_markup=cancel_menu,
    )


@dp.message(VolunteerForm.full_name)
async def get_full_name(message: types.Message, state: FSMContext):
    full_name = (message.text or "").strip()
    if len(full_name) < 3:
        await message.answer("Iltimos, F.I.Sh.ni to‘liqroq kiriting:")
        return
    await state.update_data(full_name=full_name)
    await state.set_state(VolunteerForm.age)
    await message.answer("<b>2/5.</b> Yoshingizni kiriting (masalan: 20):", parse_mode="HTML")


@dp.message(VolunteerForm.age)
async def get_age(message: types.Message, state: FSMContext):
    text = (message.text or "").strip()
    if not text.isdigit() or not (10 <= int(text) <= 100):
        await message.answer("Iltimos, yoshingizni to‘g‘ri raqamda kiriting (masalan: 18):")
        return
    await state.update_data(age=int(text))
    await state.set_state(VolunteerForm.phone)
    await message.answer(
        "<b>3/5.</b> Telefon raqamingizni yuboring (tugmani bosing yoki +998... ko‘rinishida yozing):",
        parse_mode="HTML",
        reply_markup=phone_menu,
    )


@dp.message(VolunteerForm.phone)
async def get_phone(message: types.Message, state: FSMContext):
    phone = None
    if message.contact and message.contact.phone_number:
        phone = message.contact.phone_number
    elif message.text:
        phone = message.text.strip()

    if not phone or len(phone) < 7:
        await message.answer("Iltimos, yaroqli telefon raqam kiriting yoki tugma orqali yuboring:")
        return

    await state.update_data(phone=phone)
    await state.set_state(VolunteerForm.region)
    await message.answer(
        "<b>4/5.</b> Qaysi viloyat yoki shahardansiz?",
        parse_mode="HTML",
        reply_markup=cancel_menu,
    )


@dp.message(VolunteerForm.region)
async def get_region(message: types.Message, state: FSMContext):
    region = (message.text or "").strip()
    if len(region) < 2:
        await message.answer("Iltimos, hududingizni kiriting:")
        return
    await state.update_data(region=region)
    await state.set_state(VolunteerForm.direction)
    await message.answer(
        "<b>5/5.</b> Qaysi yo‘nalishda volontyorlik qilmoqchisiz?",
        parse_mode="HTML",
        reply_markup=direction_menu,
    )


@dp.message(VolunteerForm.direction)
async def get_direction(message: types.Message, state: FSMContext):
    direction = (message.text or "").strip()
    await state.update_data(direction=direction)
    data = await state.get_data()

    preview_text = (
        "📋 <b>Ma'lumotlaringizni tekshiring:</b>\n\n"
        f"👤 <b>F.I.Sh.:</b> {data['full_name']}\n"
        f"🎂 <b>Yosh:</b> {data['age']}\n"
        f"📞 <b>Telefon:</b> {data['phone']}\n"
        f"📍 <b>Hudud:</b> {data['region']}\n"
        f"🎯 <b>Yo‘nalish:</b> {data['direction']}\n\n"
        "Barchasi to‘g‘rimi?"
    )

    await state.set_state(VolunteerForm.confirm)
    await message.answer("Tekshirish uchun ma'lumotlar:", reply_markup=ReplyKeyboardRemove())
    await message.answer(preview_text, parse_mode="HTML", reply_markup=confirm_inline)


# --- TASDIQLASH VA ADMIN'GA YUBORISH ---
@dp.callback_query(VolunteerForm.confirm, F.data == "confirm_yes")
async def confirm_yes(callback: types.CallbackQuery, state: FSMContext):
    data = await state.get_data()
    user = callback.from_user
    username_text = f"@{user.username}" if user.username else "mavjud emas"

    admin_message = (
        "🆕 <b>YANGI VOLONTYOR ARIZASI</b>\n\n"
[25.11.2026 18:36] Hasanboy Razzaqov: f"👤 <b>F.I.Sh.:</b> {data['full_name']}\n"
        f"🎂 <b>Yosh:</b> {data['age']}\n"
        f"📞 <b>Telefon:</b> {data['phone']}\n"
        f"📍 <b>Hudud:</b> {data['region']}\n"
        f"🎯 <b>Yo‘nalish:</b> {data['direction']}\n\n"
        f"🔹 <b>Telegram:</b> {username_text}\n"
        f"🆔 <b>ID:</b> <code>{user.id}</code>"
    )

    if ADMIN_ID:
        await bot.send_message(chat_id=ADMIN_ID, text=admin_message, parse_mode="HTML")

    await callback.message.edit_text(
        "✅ <b>Arizangiz qabul qilindi!</b>\n"
        "Tez orada siz bilan bog‘lanamiz.",
        parse_mode="HTML",
    )
    await callback.message.answer("Asosiy menyu:", reply_markup=main_menu)
    await state.clear()
    await callback.answer()


@dp.callback_query(VolunteerForm.confirm, F.data == "confirm_no")
async def confirm_no(callback: types.CallbackQuery, state: FSMContext):
    await state.set_state(VolunteerForm.full_name)
    await callback.message.edit_text("Ma'lumotlar tozalandi. Qaytadan boshlaymiz.")
    await callback.message.answer(
        "<b>1/5.</b> Familiya, ism va sharifingizni kiriting:",
        parse_mode="HTML",
        reply_markup=cancel_menu,
    )
    await callback.answer()


async def main():
    await set_commands(bot)
    await dp.start_polling(bot)


if name == "main":
    asyncio.run(main())
