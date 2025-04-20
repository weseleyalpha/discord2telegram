import discord
import requests
import os

DISCORD_TOKEN = os.environ.get("DISCORD_TOKEN")

# Format: {DiscordChannelID: TelegramChatID}
CHANNEL_TO_TELEGRAM = {
    int(os.environ.get("DISCORD_CHANNEL_1")): os.environ.get("TELEGRAM_CHAT_1"),
    int(os.environ.get("DISCORD_CHANNEL_2")): os.environ.get("TELEGRAM_CHAT_2"),
}

intents = discord.Intents.default()
intents.messages = True

client = discord.Client(intents=intents)

@client.event
async def on_ready():
    print(f"Logged in as {client.user}")

@client.event
async def on_message(message):
    channel_id = message.channel.id
    if channel_id in CHANNEL_TO_TELEGRAM and not message.author.bot:
        text = f"{message.author.name}: {message.content}"
        telegram_chat_id = CHANNEL_TO_TELEGRAM[channel_id]
        telegram_token = os.environ.get("TELEGRAM_TOKEN")
        url = f"https://api.telegram.org/bot{telegram_token}/sendMessage"
        requests.post(url, json={"chat_id": telegram_chat_id, "text": text})

client.run(DISCORD_TOKEN)
