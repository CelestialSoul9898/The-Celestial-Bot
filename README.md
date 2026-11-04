
import discord
from discord.ext import commands, tasks
import os
import aiohttp
import xml.etree.ElementTree as ET

TOKEN = os.getenv("1567541839702138920")
YOUTUBE_CHANNEL_ID = os.getenv("https://www.youtube.com/@WSoulBlox")
UPLOADS_CHANNEL_ID = int(os.getenv("https://discord.com/channels/1556844570229088326/1556844571642564661", "0"))

intents = discord.Intents.default()
bot = commands.Bot(command_prefix="!", intents=intents)

last_video = None

@bot.event
async def on_ready():
    print(f"Logged in as {bot.user}")
    await bot.tree.sync()
    if not youtube_checker.is_running():
        youtube_checker.start()

@bot.tree.command(name="rules", description="Display the server rules")
async def rules(interaction: discord.Interaction):
    embed = discord.Embed(
        title="🌌 THE CELESTIAL EMPERORS — RULES",
        description=(
            "1. Respect everyone.\n"
            "2. No harassment or discrimination.\n"
            "3. No spamming or flooding chats.\n"
            "4. No advertising without permission.\n"
            "5. No scams, exploits or malicious links.\n"
            "6. Keep conversations in the correct channels.\n"
            "7. Listen to staff and follow their instructions.\n"
            "8. Have fun and enjoy the server!"
        ),
        color=discord.Color.purple()
    )
    await interaction.response.send_message(embed=embed)

@tasks.loop(minutes=5)
async def youtube_checker():
    global last_video

    if not YOUTUBE_CHANNEL_ID or not UPLOADS_CHANNEL_ID:
        return

    url = (
        "https://www.youtube.com/feeds/videos.xml"
        f"?channel_id={https://www.youtube.com/watch?v={https://www.youtube.com/@WSoulBlox}"
    )

    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            xml = await response.text()

    root = ET.fromstring(xml)
    ns = {
        "atom": "http://www.w3.org/2005/Atom",
        "yt": "http://www.youtube.com/xml/schemas/2015"
    }

    entry = root.find("atom:entry", ns)
    if entry is None:
        return

    video_id = entry.find("yt:videoId", ns).text

    if video_id == last_video:
        return

    last_video = video_id

    title = entry.find("atom:title", ns).text
    link = f"https://www.youtube.com/watch?v={video_id}"

    channel = bot.get_channel(UPLOADS_CHANNEL_ID)
    if channel:
        await channel.send(
            f"🌌 **NEW CELESTIAL UPLOAD!**\n"
            f"🎬 **{title}**\n"
            f"{link}\n"
            f"Go show some support!"
        )

@youtube_checker.before_loop
async def before_checker():
    await bot.wait_until_ready()

bot.run(1567541839702138920)
# The-Celestial-Bot
CELESTIAL BOT
