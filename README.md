**Pagination Using buttons in discord.js V13**

**1:** Make a file named `pagination.js` and paste the code below into said file

```js
const {
    MessageActionRow,
    Message,
    MessageEmbed,
    MessageButton,
  } = require("discord.js");
  
  async function button_pagination(message, embeds) {
    if (!message || !embeds) throw new Error(console.log('Please provide all arguments, make sure they are valid'))
  
    let index = 0;
  
    let button = new MessageActionRow()
        .addComponents(
            new MessageButton().setCustomId(`previous`) //button to go back a page
            .setLabel('Previous Page').setEmoji("◀️").setStyle('SECONDARY'),
  
            new MessageButton().setCustomId(`home`) //home button to go back to the main menu
            .setLabel('Home Page').setEmoji("🏠").setStyle("SECONDARY"),
  
            new MessageButton().setCustomId(`next`) //button to go forward a page
            .setLabel('Next Page').setEmoji("▶️").setStyle('SECONDARY'),
        );
  
    let buttons = [
        button         
    ]
  
    let msg = await message.channel.send({
        embeds: [embeds[0]],
        components: buttons       //add the buttons to the message
    }).then((message) => {
  
        const buttonID = [`previous`, `home`, `next`]; //array of button IDs
  
        const buttons = async (interaction) => {
          if (interaction.user.id === message.author.id) return interaction.reply({
            content: "Only the button owner can use these",  
            ephemeral: true,
          }); //if the user is not the author of the message, tell them and do nothing
  
            if (!buttonID.includes(interaction.customId)) return;
  
            if (interaction.customId == `previous`) {
                index = index > 0 ? --index : embeds.length - 1;
                await interaction.deferUpdate();
                    
                await interaction.message.edit({
                    embeds: [embeds[index]]
                });
  
                
  
            } else if (interaction.customId == `home`) {
                index = 0;
                await interaction.deferUpdate();
                                                  
                await interaction.message.edit({
                    embeds: [embeds[index]]
                });
  
            } else if (interaction.customId == `next`) {
  
                index = index + 1 < embeds.length ? ++index : 0;
                await interaction.deferUpdate();
  
                await interaction.message.edit({
                    embeds: [embeds[index]]
                });
            } 
        };
  
        const filter = (interaction) => {
            return !interaction.user.bot   //if the user is a bot, do nothing
        };
  
        const collector = message.createMessageComponentCollector({
            filter,              //filter out bots
            componentType: "BUTTON",       //only collect buttons
            time: 120000         //collect for 2 minutes if nothing is pressed for 2 minutes disable the buttons
        });
  
        collector.on("collect", buttons);
        collector.on("end", () => {                   
            button.components[0].setDisabled(true)    //disable the previous button
            button.components[1].setDisabled(true)    //disable the home button
            button.components[2].setDisabled(true)    //disable the next button
  
            message.edit({
                components: [button]      //add the buttons to the message and remove old ones
            })
        });
    });
  
    return msg;
  
  }
  
module.exports = button_pagination;
```

**2:** Head to your `index.js` file or your main file might be named `bot.js` and add this line `require('path to pagination.js')(client);`
An example would be like this `require('../Handlers/Pagination.js')(client);`

**3:** To use this require it like so `const button_pagination = require('path to the location of file');`

**Make sure to add the following code at the bottom of the file where you have your embeds and swap them from embed1, embed2, embed3 to the names of your embeds and that should be it**

```js
            //add your embeds here in a list 
        list = [
            embed1,
            embed2,
            embed3
        ];

        button_pagination(message, list)
```
