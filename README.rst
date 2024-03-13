import discord
from discord.ext import commands

bot = commands.Bot(command_prefix='!')

allowed_ranks = ["عسكري"]

@bot.command()
async def create_military_id(ctx):
    user_roles = [role.name for role in ctx.author.roles]
    if not any(role in allowed_ranks for role in user_roles):
        await ctx.send("ليس لديك الصلاحية لاستخدام هذا الأمر.")
        return

    await ctx.author.send("يرجى إرسال الاسم:")
    name = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    await ctx.author.send("يرجى إرسال اسم المدينة:")
    city = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    await ctx.author.send("يرجى إرسال تاريخ الميلاد (يوم/شهر/سنة):")
    dob = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    await ctx.author.send("يرجى إرسال التسلسل العسكري:")
    military_id = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    await ctx.author.send("يرجى إرسال الرتبة:")
    rank = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    await ctx.author.send("يرجى إرسال رابط الصورة (اختياري):")
    image_url = await bot.wait_for('message', check=lambda message: message.author == ctx.author)

    if image_url.content.lower() == 'skip':
        image_url = None
    else:
        image_url = image_url.content

    await create_card(ctx, name.content, city.content, dob.content, military_id.content, rank.content, image_url)

async def create_card(ctx, name, city, dob, military_id, rank, image_url=None):
    if image_url is None:
        cover_image = "https://example.com/military_cover_image.png"
    else:
        cover_image = image_url

    embed = discord.Embed(title="بطاقة عسكرية", description=f"Name: {name}\nCity: {city}\nDate of Birth: {dob}\nMilitary ID: {military_id}\nRank: {rank}", color=0x4169E1)

    embed.set_image(url="https://example.com/watermark.png")

    embed.set_footer(text="وزارة الداخلية")

    embed.set_thumbnail(url=cover_image)

    officer_ranks = ["ملازم", "ملازم أول", "نقيب", "رائد", "مقدم", "عقيد", "عميد", "لواء", "فريق"]
    if rank in officer_ranks:
        embed.title = "بطاقة ضباط"

    await ctx.author.send(embed=embed)

@bot.event
async def on_message(message):
    if message.author == bot.user:
        return
    if message.content.lower() == "ارسال نموذج انشاء بطاقة عسكرية":
        await create_military_id(message.channel)

    await bot.process_commands(message)

bot.run('MTIxNzQ2MTYwNDcyNDExNzU5NQ.Gmo7R8.VtjZq4fr9Cb8kyWiOA0d8m4FXQNHQLl1t_tiUs')
