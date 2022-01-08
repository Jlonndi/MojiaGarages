# Change Log
## Date: 08/01/22
- Automatically save coordinates, vehicle status
- Auto spawn car in its last position, if there are players nearby
- Depot only contains vehicles with fines greater than 0
## Date: 31/12/21
- Show only the necessary zones
- Delete unnecessary zones every time there is a change (job, gang...)
- Set house key check event before garage update event to fix incorrect garage update every time job change
- Update event to check house key:
Need Edit qb-houses\server\main.lua:
```
RegisterNetEvent('qb-houses:server:buyHouse', function(house)
    local src = source
    local pData = QBCore.Functions.GetPlayer(src)
    local price = Config.Houses[house].price
    local HousePrice = math.ceil(price * 1.21)
    local bankBalance = pData.PlayerData.money["bank"]

    if (bankBalance >= HousePrice) then
        houseowneridentifier[house] = pData.PlayerData.license
        houseownercid[house] = pData.PlayerData.citizenid
        housekeyholders[house] = {
            [1] = pData.PlayerData.citizenid
        }
        exports.oxmysql:insert('INSERT INTO player_houses (house, identifier, citizenid, keyholders) VALUES (?, ?, ?, ?)',{house, pData.PlayerData.license, pData.PlayerData.citizenid, json.encode(housekeyholders[house])})
        exports.oxmysql:execute('UPDATE houselocations SET owned = ? WHERE name = ?', {1, house})
        TriggerClientEvent('qb-houses:client:SetClosestHouse', src)
        pData.Functions.RemoveMoney('bank', HousePrice, "bought-house") -- 21% Extra house costs
        TriggerEvent('qb-bossmenu:server:addAccountMoney', "realestate", (HousePrice / 100) * math.random(18, 25))
        TriggerEvent('qb-log:server:CreateLog', 'house', 'House Purchased:', 'green', '**Address**:\n'..house:upper()..'\n\n**Purchase Price**:\n$'..HousePrice..'\n\n**Purchaser**:\n'..pData.PlayerData.charinfo.firstname..' '..pData.PlayerData.charinfo.lastname)
		TriggerClientEvent("MojiaGarages:client:updateGarage", -1) 	-- Update Garages	
	else
        TriggerClientEvent('QBCore:Notify', source, "You dont have enough money..", "error")
    end
end)
```
## Date: 30/12/21
**Added:**
- Change Log