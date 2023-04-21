import io

import os

from google.cloud import vision

from google.cloud.vision import types

# توکن ربات خود را در این قسمت وارد کنید

TOKEN = 'YOUR_TOKEN_HERE'

# کلید API Google Vision را در این قسمت وارد کنید

os.environ['GOOGLE_APPLICATION_CREDENTIALS'] = 'PATH_TO_KEY_FILE.json'

def ocr(update, context):

    # دریافت عکس از کاربر

    photo_file = context.bot.getFile(update.message.photo[-1].file_id)

    # تبدیل عکس به بایت‌ها

    content = photo_file.download_as_bytearray()

    # ایجاد یک instance از کلاینت Google Vision

    client = vision.ImageAnnotatorClient()

    # تبدیل بایت‌ها به یک instance از کلاس Image

    image = types.Image(content=content)

    # فراخوانی سرویس OCR

    response = client.text_detection(image=image)

    texts = response.text_annotations

    # دریافت متن تشخیص داده شده و ارسال پاسخ به کاربر

    if texts:

        message_text = texts[0].description

    else:

        message_text = "متنی شناسایی نشد!"

    context.bot.send_message(chat_id=update.effective_chat.id, text=message_text)

def main():

    updater = Updater(TOKEN, use_context=True)

    dp = updater.dispatcher

    dp.add_handler(MessageHandler(Filters.photo, ocr))

    updater.start_polling()

    updater.idle()

if __name__ == '__main__':

    main()را
