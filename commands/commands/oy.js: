const fs = require('fs');
const { EmbedBuilder } = require('discord.js');

module.exports = {
  name: 'oy',
  description: 'Oy kullanma işlemi',
  execute(message, args) {
    let database = {};
    if (fs.existsSync('./database.json')) {
      database = JSON.parse(fs.readFileSync('./database.json'));
    } else {
      database.db = [];
    }

    const serverIndex = database.db.findIndex(server => server.serverID === message.guild.id);
    if (serverIndex === -1) {
      message.reply(`<:4974discordcreatecategorywhite:1087445344796815472> **${message.author.tag}** Bu sunucuda oy kanalı ayarlanmadığından oy kullanamazsınız!`);
      return;
    }
    if (message.channel.id !== database.db[serverIndex].voteChannel) {
      message.reply(`<:4974discordcreatecategorywhite:1087445344796815472> Bu komut sadece <#${database.db[serverIndex].voteChannel}> kanalında kullanılabilir. Lütfen doğru kanalı kullanın.`);
      return;
    }

    const userIndex = database.db[serverIndex].votes.findIndex(vote => vote.userID === message.author.id);
    const ONE_HOUR = 60 * 60 * 1000; // 1 saat

    if (userIndex !== -1) {
      const lastVoteTime = database.db[serverIndex].votes[userIndex].lastVoteTime;
      if (Date.now() - lastVoteTime < ONE_HOUR) {
        const remainingTime = new Date(ONE_HOUR - (Date.now() - lastVoteTime)).toISOString().substr(11, 8);
        message.reply(`<:8719logstageminusd:1087445336005558362> Zaten oy kullandınız **${message.author.tag}**! Tekrar oy kullanabilmek için **${remainingTime}** sonra tekrar deneyin.`);
        return;
      }
      database.db[serverIndex].votes[userIndex].lastVoteTime = Date.now();
      database.db[serverIndex].votes[userIndex].votePoints += database.db[serverIndex].serverVotePoints;
      database.db[serverIndex].serverVotePoints += 1;
    } else {
      database.db[serverIndex].votes.push({
        userID: message.author.id,
        votePoints: database.db[serverIndex].serverVotePoints,
        lastVoteTime: Date.now()
      });
      database.db[serverIndex].serverVotePoints += 1;
    }
    fs.writeFileSync('./database.json', JSON.stringify(database));
    message.reply(`<:2885discordmessagerequestswhite:1087449389116698745> **${message.guild.name}**, adlı sunucuya başarıyla oy verdiniz **${message.author.tag}**. Oyunuz bizim için çok değerli.`);
  }
};
