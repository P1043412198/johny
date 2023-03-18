require "lib.moonloader"
local ffi = require "ffi"
local imgui = require 'mimgui'
local encoding = require 'encoding' 
local ad = require "ADDONS"
require "lib.sampfuncs"
local vk = require 'vkeys'
local memory = require 'memory'
local iconv = require("iconv")
local new = imgui.new
local rkeys = require 'rkeys'
local samp = require 'lib.samp.events'
local mimgui_addons = require 'mimgui_addons'
local json = require("json")
local bulletPoints = {}

local checkbox_state = false
local CheckBoxWorkOff = false
local CheckBoxWorkNews = false

local selected_row = nil


local button_names = {}
local logwarning = false
local outputText = "0"
local colortext = "{00FF00} зеленый, {7CFC00} лайм, {FF0000} красный, {FFFFFF} белый, {0000FF} синий. \n{00FFFF} голубой. {800080} фиолетовый. {FFFF00} желтый, {FF00FF} розовый"


local ActiveMenu = {
    v = {vk.VK_F2}
}
local hotkeys = {}
local hotkeyz = {}
local bulletColor = 0xffff0000
local bindID = 0
------------------
y = 1
ccom = 1
zom = 1
fm = 1
--------------
local fmenu_settings = {
{statusf = {value = false}, key = {nil,nil}},
{name = "Свободный слот", delay = 1000, text = "Ваш текст"}
}

local button_settings = {
  {name = "Свободный слот", key = {nil,nil}, delay = 1000, text = "Ваш текст", statusbutton = {value = false}}
}
local command_setting = {
  {command =  "example", name = "Свободный слот 1", delay = 1000, text = "Ваш текст", statuscommand = {value = false}}
}
local time_setting = {
  {time =  "18:00:00", name = "Свободный слот 1", delay = 1000, text = "Ваш текст", statustime = {value = false}}
}
local hotkeys = {}
--------------------
local buffer = imgui.new.char[256]()
local input_text_buf = imgui.new.char[24456]()
local input_com_buf = imgui.new.char[256]()
local input_mtext_buf = imgui.new.char[25556]()
local input_maincom_buf = imgui.new.char[256]()
local input_maincomtext_buf = imgui.new.char[25006]()
local input_time_buf = imgui.new.char[256]()
local input_timename_buf = imgui.new.char[256]()
local input_timetext_buf = imgui.new.char[25006]()
local input_fmenutext_buf = imgui.new.char[25006]()
local input_fmenuname_buf = imgui.new.char[25006]()
local input_news_buf = imgui.new.char[25006]()
local input_news1_buf = imgui.new.char[25006]()
local input_news2_buf = imgui.new.char[25006]()
local input_news3_buf = imgui.new.char[101]()
local BulletTime = imgui.new.int(5)
local BulletWH = imgui.new.int(2)
clear_color = new.float[3](0.45, 0.55, 0.60)
BulletColorObject = new.float[4](0, 0, 0, 255)
-------------------------
encoding.default = 'CP1251' 
u8 = encoding.UTF8
------------------------------------------
local enasos = false
local frame_created = false
local frames_created = false
local frameskey_created = false
local framescommand_created = false
local framestime_created = false
local framesfmenu_created = false
local speedmenu_created = false
local mainfmenu_created = false
local flag = false -- флаг, который указывает, был ли выполнен цикл
-------Captlog-------------
local frame_captlog = false
local captlog = false
local window_captlog = ffi.new("bool[1]", captlog)
----------------------------
local frame_copmenu = false
local copmenu = false
local window_copmenu = ffi.new("bool[1]", copmenu)
--Окно 2
local frame_copmenu2 = false
local copmenu2 = false
local window_copmenu2 = ffi.new("bool[1]", copmenu2)
---------------------------------------------
local window_open = false
local open = true
local openbindmenukey = true
local menucommand = true
local opentime = true
local openfastmenu = true
local mainfastmenu = true
local speedmenu = true
----------------------------------------------
local window_open_ptr = ffi.new("bool[1]", window_open)
local window_open_pan = ffi.new("bool[1]", open)
local window_openbindmenukey = ffi.new("bool[1]", openbindmenukey)
local window_menucommand = ffi.new("bool[1]", menucommand)
local window_opentime = ffi.new("bool[1]", opentime)
local window_openfastmenu = ffi.new("bool[1]", openfastmenu)
local window_mainfastmenu = ffi.new("bool[1]", mainfastmenu)
local window_speedmenu = ffi.new("bool[1]", speedmenu)
-------------------------------------------------

local toggle = imgui.new.bool(false)
local toggle1 = imgui.new.bool(false)
local toggle2 = imgui.new.bool(false)
local toggle3 = imgui.new.bool(false)
local toggle4 = imgui.new.bool(false)
local toggle5 = imgui.new.bool(false)
local toggle6 = imgui.new.bool(false)


local button_names = {} 
local shoots_log = {} 

local function waitForSamp()
  while not isSampAvailable() do
    wait(0)
  end
end
imgui.OnInitialize(function()
    imgui.GetIO().IniFilename = nil
    local glyph_ranges = imgui.GetIO().Fonts:GetGlyphRangesCyrillic()
    imgui.GetIO().Fonts:AddFontFromFileTTF(getFolderPath(0x14) .. '\\trebucbd.ttf', 14.0, nil, glyph_ranges)
    smal = imgui.GetIO().Fonts:AddFontFromFileTTF(getFolderPath(0x14) .. '\\trebucbd.ttf', 13.0, _, glyph_ranges)
    big = imgui.GetIO().Fonts:AddFontFromFileTTF(getFolderPath(0x14) .. '\\trebucbd.ttf', 16.0, _, glyph_ranges)
	biglogo = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo8.png')
	logokey = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo4.png')
	logocom = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo5.png')
	logotime = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo6.png')
	logonot = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo7.png')
	mainlogo = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logo2.png')
	minlogo = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\minlogo.png')
	coplogo = imgui.CreateTextureFromFile(getGameDirectory() .. '\\moonloader\\logo\\logofbi.png')

end)

local button_count = 0

function onButtonClick()
  button_count = button_count + 1
  imgui.Button("Button " .. button_count)
end


function logotype()
  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 200
  local window_height = 100
  local window_x = (screenWidth - window_width) - 100
  local window_y = (screenHeight - window_height) - 100
local newFrame = imgui.OnFrame( --[[Сама функция создания фрейма, их может быть сколько вашей душе угодно.
                                    Обратите внимание, что в mimgui рекомендуется создавать отдельный
                                    фрейм для каждого окна, однако это не является обязательным.]]
    function() return true end, -- Определяет, выполняется/отображается ли текущий фрейм.
    function(player)            --[[Сама область, в которой уже будем рисовать элементы.
                                    В функцию в качестве первой переменной передаются список функций
                                    для взаимодействия с локальным игроком и рядом нескольких возможностей.]]
									player.HideCursor = true
									    local window_size = imgui.ImVec2(window_width, window_height)
	      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
      imgui.SetNextWindowBgAlpha(0)
      imgui.Begin("Бинд клавиш5",nil, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)-- Создаём новое окно с заголовком 'Main Window'         -- Добавляем туда текст 'Hello'
        		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("SA-Assistant v.0.0.11")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("SA-Assistant v.0.0.11"))        -- Добавляем туда текст 'Hello'
				imgui.SetCursorPosX((imgui.GetWindowWidth() - 60) / 2); 
		imgui.Image(minlogo, imgui.ImVec2(60, 60))
		imgui.End()                 -- Объявляем конец данного окна
    end
)
end



----CAPTLOG---------------------
function captlogmenu()
    captlog = not window_captlog[0]
    window_captlog[0] = captlog
    local screenWidth, screenHeight = getScreenResolution()
	local total_shots = 0
local hit_shots = 0
local miss_shots = 0
local accuracy = 0
local best_hit_series = 0
    local window_width = 800
    local window_height = 380
    local window_x = (screenWidth - window_width) / 1.14
    local window_y = (screenHeight - window_height) / 6
    local selected_player_name -- добавить эту строку
    if captlog and not frame_captlog then
        frame_captlog = true
        local newFrame = imgui.OnFrame(
            function() return captlog end,
            function(player)
                player.HideCursor = true
                local window_size = imgui.ImVec2(window_width, window_height)
                imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
                local window_pos = imgui.ImVec2(window_x, window_y)
                imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
                imgui.SetNextWindowBgAlpha(0.8)
                imgui.Begin("Captlog", window_captlog, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
                imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("SA-Assistant | LOG SHOOTS")).x) / 2)
                imgui.TextColored({1.0, 1, 1, 1.0}, u8("SA-Assistant | LOG SHOOTS"))
                imgui.Spacing()
                imgui.Separator()
                local unique_players = {}
                for _, v in ipairs(shoots_log) do
                    if not unique_players[v.name] then
                        unique_players[v.name] = true
                    end		
                end
                imgui.BeginChild("AACaptlog", imgui.ImVec2(200, 180), true)
                for player_name, _ in pairs(unique_players) do
                    if imgui.Selectable(player_name) then
                        selected_player_name = player_name
                    end
                end
				if selected_player_name then
    total_shots = 0
    hit_shots = 0
    miss_shots = 0
    accuracy = 0
    best_hit_series = 0
    local current_hit_series = 0
    for i, v in ipairs(shoots_log) do
        if v.name == selected_player_name then
            total_shots = total_shots + 1
            if v.types == 1 then -- попадание
                hit_shots = hit_shots + 1
                current_hit_series = current_hit_series + 1
                if current_hit_series > best_hit_series then
                    best_hit_series = current_hit_series
                end
            else -- промах
                miss_shots = miss_shots + 1
                current_hit_series = 0
            end
        end
    end
    accuracy = hit_shots / total_shots * 100
    accuracy = accuracy ~= accuracy and 0 or accuracy -- проверка на NaN
end
                imgui.EndChild()
                imgui.SameLine()
 imgui.BeginChild("NewLogCa", imgui.ImVec2(550, 180), true)
                for _, row in ipairs(shoots_log) do
                    if row.name == selected_player_name then -- TODO: Replace with the actual selected player name
    imgui.Text(row.name .. "[" .. row.nameid.."]["..row.hpname.."] - "..row.types.." - "..row.dist.." - "..row.namesh)
	imgui.SetScrollHereY()
                    end
					        if row.namesh == selected_player_name then
           imgui.Text(row.name .. "[" .. row.nameid.."]["..row.hpname.."] - "..row.types.." - "..row.dist.." - "..row.namesh)
		   imgui.SetScrollHereY()
        end
                end
                imgui.EndChild()
				imgui.Spacing()
				imgui.Separator()
				imgui.Spacing()
				                imgui.BeginChild("Aimtimer", imgui.ImVec2(400, 110), true)
				imgui.Text(u8("Количество выстрелов: ") .. total_shots)
imgui.Text(u8("Количество попаданий: ") .. hit_shots)
imgui.Text(u8("Количество промахов: ") .. miss_shots)
imgui.Text(u8("Точность: ") .. string.format("%.2f", accuracy) .. "%")
imgui.Text(u8("Лучшая серия попаданий: ") .. best_hit_series)
                imgui.EndChild()
																if imgui.Button(u8'Очистить таблицу',imgui.ImVec2(250, 24)) then
																shoots_log = {}
				end
				imgui.SameLine()
																		if imgui.Button(u8'Таблица урона',imgui.ImVec2(250, 24)) then
																		  selected_player_name = nil
				end
				imgui.SameLine()
																		if imgui.Button(u8'Полный лог',imgui.ImVec2(250, 24)) then
				sampSendChat('/light')
				end
                imgui.End()
            end
        )
    end
end


---------------------



----- COPMENU ---
function maincopmenu()
copmenu = not copmenu
window_copmenu[0] = copmenu

  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 800
  local window_height = 410
  local window_x = (screenWidth - window_width) / 1.14
  local window_y = (screenHeight - window_height) / 6
  
if copmenu and not frame_copmenu then
frame_copmenu = true
local newFrame = imgui.OnFrame(
function() return copmenu end,
function(player)  
   local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
            imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.0, 0, 0, 1))
	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
				imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.1, 0.1, 0.1, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.0, 0.00, 0.0, 1.00))
      imgui.SetNextWindowBgAlpha(0.8)                    	--[[Сама область, в которой уже будем рисовать элементы.
                                    В функцию в качестве первой переменной передаются список функций
                                    для взаимодействия с локальным игроком и рядом нескольких возможностей.]]
        imgui.Begin("Copmenu",window_copmenu, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
		      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
				imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100) 
	  	  		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Рабочая область управления объявлениями | Новости")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Рабочая область управления объявлениями | Новости")) 
imgui.InputTextMultiline(u8 "##139", input_news3_buf, ffi.sizeof(input_news3_buf),imgui.ImVec2(785, 35))
        local input_news3_string = ffi.string(input_news3_buf)
        local input_news3_len = string.len(input_news3_string)
        imgui.Text(u8("Количество символов составляет: ") .. input_news3_len, 0xFFFFFFFF)
		imgui.InputTextMultiline(u8 "##138", input_news_buf, ffi.sizeof(input_news_buf),imgui.ImVec2(785, 175))
		imgui.NewLine()
		ffi.copy(input_news2_buf, colortext, #colortext)
		imgui.InputTextMultiline("##202", input_news2_buf, 25006, imgui.ImVec2(785, 50))
			imgui.Spacing()
    imgui.ColorEdit3("clear color", clear_color) -- Edit 3 floats representing a color
	imgui.Spacing()
    ffi.copy(input_news1_buf, outputText, #outputText)
    imgui.InputTextMultiline("##201", input_news1_buf, 25006, imgui.ImVec2(785, 35))
		imgui.PopStyleColor(2)
        imgui.End()                 -- Объявляем конец данного окна
    end
)
end

end


function windowfastmenu()
  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 300
  local window_height = 352
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2


if mainfastmenu and not mainfmenu_created then
mainfmenu_created = true
local newFrame = imgui.OnFrame(
function() return mainfastmenu end,
function(player)           
    local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
            imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
				imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.5, 0.07, 0.05, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100)                    
               
        imgui.Begin("Быстрое меню/Управление##2",window_mainfastmenu, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)

for i = 1, 10 do	
  if imgui.Button(u8(fmenu_settings[i+1].name), imgui.ImVec2(-1, 30)) then
    sendChatWithDelay(fmenu_settings[i+1].text, fmenu_settings[fm+1].delay)
  end
end        -- Добавляем туда текст 'Hello'
        imgui.End()  
imgui.PopStyleColor(2)		-- Объявляем конец данного окна
    end)
	end


end

function speedometr()
        local sw, sh = getScreenResolution()
	    local btn_size = imgui.ImVec2(-0.1, 21)


if speedmenu and not speedmenu_created then
speedmenu_created = true
local newFrame = imgui.OnFrame(
function() return speedmenu end,
function(player)        
player.HideCursor = true      --[[Сама область, в которой уже будем рисовать элементы..]]
		         imgui.SetNextWindowSize(imgui.ImVec2(790, 170), imgui.Cond.FirstUseEver)
		imgui.SetNextWindowPos(imgui.ImVec2((sw / 2), sh / 1.13), imgui.Cond.FirstUseEver, imgui.ImVec2(0.5, 0.5))
                imgui.Begin("Спидометр",window_speedmenu, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
		imgui.SetCursorPosX(10)
		texten = sampTextdrawGetString( engine_textdraw_id)
		if string.find(texten,'Engine') then
				imgui.BeginChild("3", imgui.ImVec2(280, 110), true)
				imgui.Text('Топливный бак:')
				imgui.Image(fuellogobig, imgui.ImVec2((64),64))
				imgui.SameLine()
				imgui.SetCursorPosY(35)
				imgui.SetCursorPosX(100)
							text = sampTextdrawGetString( fuel_textdraw_id):match("(%d+)")
				if text == nil then
				text = u8('0')
				end
				imgui.PushFont(speedfontsize)
				imgui.Text(''..text..(' %'))
				imgui.PopFont()
			imgui.EndChild()
				imgui.SameLine()
				imgui.BeginChild("2", imgui.ImVec2(200, 110), true)
				imgui.PushFont(speedfontname)
					imgui.Text('Спидометр:')
					imgui.PopFont()
		imgui.PushFont(speedfontsize)
		tostring(speed)
			speed = ("%.0f"):format(speed)
			imgui.Text(''..speed)
			imgui.PopFont()
		imgui.PushFont(speedfontname)
					imgui.Text('км/ч')
					imgui.Spacing()
					imgui.PopFont()
				imgui.EndChild() 
								imgui.SameLine()
								imgui.BeginChild("Zd", imgui.ImVec2(280, 110), true)
								imgui.Text('Двигатель:')
								imgui.Image(enginelogobig, imgui.ImVec2((64),64))
												imgui.SetCursorPosY(35)
				imgui.SetCursorPosX(120)
				imgui.PushFont(speedfontsize)
				imgui.Text(u8('ON'))
				imgui.PopFont()
				imgui.EndChild()
				imgui.Spacing()
				imgui.SetCursorPosX(10)
				imgui.BeginChild("2534", imgui.ImVec2(270, 40), true)
								textm = sampTextdrawGetString( mile_textdraw_id):match("(%d+)")
				if textm == nil then
				textm = u8('0')
				end
				imgui.Text('Пробег т/с: '..textm..' км.')
				imgui.EndChild()
				imgui.SameLine()
				text = tonumber(text)
				imgui.BeginChild("234", imgui.ImVec2(220, 40), true)
				if text <= 200 and text > 70 then
				imgui.Image(fuellogo, imgui.ImVec2((24),24))
				else
				    if text <= 70 and text > 50 then
				    imgui.Image(fuellogo50, imgui.ImVec2((24),24))
					else
					    if text <= 50 and text > 20 then
						imgui.Image(fuellogo20, imgui.ImVec2((24),24))
						else
						if text <= 20 and text >= 0 then
						imgui.Image(fuellogoff, imgui.ImVec2((24),24))
						end
						end
					end
				end
				imgui.SameLine()
				imgui.Image(enginelogo100, imgui.ImVec2((24),24))
						imgui.SameLine()
				imgui.Image(beltlogo, imgui.ImVec2((24),24))
						imgui.SameLine()
								if health < 4000 and health > 800 then
				imgui.Image(repairlogo100, imgui.ImVec2((24),24))
				else 
				    if health <= 800 and health > 600 then
					imgui.Image(repairlogo50, imgui.ImVec2((24),24)) 
					else 
					    if health <= 600 and health > 400 then
						imgui.Image(repairlogo30, imgui.ImVec2((24),24))
						else
						    if health <= 400 and health > 0 then
							imgui.Image(repairlogoff, imgui.ImVec2((24),24))
							end
						end
					end
				end
						imgui.SameLine()
						textlight2 = sampTextdrawGetString( light2_textdraw_id)
		           textlight = sampTextdrawGetString( light_textdraw_id)	
                if string.find(textlight,'Light') then				   
				imgui.Image(lightlogobl, imgui.ImVec2((24),24))
				else
	    	    if string.find(textlight2,'Light') then
				imgui.Image(lightlogodl, imgui.ImVec2((24),24))
				else
				imgui.Image(lightlogoff, imgui.ImVec2((24),24))
				end
				end
										imgui.SameLine()
				textlimit = sampTextdrawGetString( limit_textdraw_id)						
				if string.find(textlimit, 'Limit') then
				
				imgui.Image(speedlogoff, imgui.ImVec2((24),24))
				else
				imgui.Image(speedlogoon, imgui.ImVec2((24),24))
				end
														imgui.SameLine()
				imgui.Image(keylogo, imgui.ImVec2((24),24))
				imgui.EndChild()
								imgui.SameLine()
				imgui.BeginChild("2354", imgui.ImVec2(270, 40), true)
				imgui.PushStyleVar(imgui.StyleVar.FrameRounding, 1)
				if imgui.Button(u8'A',imgui.ImVec2(40, 24)) then
				sampSendChat('/en')
				end
				imgui.SameLine()
								if imgui.Button(u8'B',imgui.ImVec2(40, 24)) then
				sampSendChat('/light')
				end
				imgui.SameLine()
				if imgui.Button(u8'C',imgui.ImVec2(40, 24)) then
				sampSendChat('/lem')
				end
				imgui.SameLine()
				if imgui.Button(u8'D',imgui.ImVec2(40, 24)) then
				sampSendChat('/loff')
				end
								imgui.SameLine()
								if imgui.Button(u8'E',imgui.ImVec2(40, 24)) then
				sampSendChat('/lock')
				end
								imgui.SameLine()
								if imgui.Button(u8'F',imgui.ImVec2(40, 24)) then
				sampSendChat('/cm')
				end
                imgui.PopStyleVar()			
				imgui.EndChild()
		else  --- Выключенный двигатель!
				imgui.BeginChild("3", imgui.ImVec2(280, 110), true)
				imgui.Text('{e74c3c}Топливный бак:')
				imgui.Image(fuellogobigoff, imgui.ImVec2((64),64))
				imgui.SameLine()
				imgui.SetCursorPosY(35)
				imgui.SetCursorPosX(100)
							text = sampTextdrawGetString( fuel_textdraw_id):match("(%d+)")
				if text == nil then
				text = u8('0')
				end
				imgui.PushFont(speedfontsize)
				imgui.Text('{e74c3c}'..text..(' %'))
				imgui.PopFont()
			imgui.EndChild()
				imgui.SameLine()
				imgui.BeginChild("2", imgui.ImVec2(200, 110), true)
				imgui.PushFont(speedfontname)
					imgui.Text('{e74c3c}Спидометр:')
					imgui.PopFont()
		imgui.PushFont(speedfontsize)
		tostring(speed)
			speed = ("%.0f"):format(speed)
			imgui.Text('{e74c3c}'..speed)
			imgui.PopFont()
		imgui.PushFont(speedfontname)
					imgui.Text('{e74c3c}км/ч')
					imgui.Spacing()
					imgui.PopFont()
				imgui.EndChild() 
								imgui.SameLine()
								imgui.BeginChild("Zd", imgui.ImVec2(280, 110), true)
								imgui.Text('{e74c3c}Двигатель:')
								imgui.Image(enginelogobigoff, imgui.ImVec2((64),64))
												imgui.SetCursorPosY(35)
				imgui.SetCursorPosX(120)
				imgui.PushFont(speedfontsize)
				imgui.Text(u8('{e74c3c}OFF'))
				imgui.PopFont()
				imgui.EndChild()
				imgui.Spacing()
				imgui.SetCursorPosX(10)
				imgui.BeginChild("2534", imgui.ImVec2(270, 40), true)
								textm = sampTextdrawGetString( mile_textdraw_id):match("(%d+)")
				if textm == nil then
				textm = u8('0')
				end
				imgui.Text('{e74c3c}Пробег т/с: '..textm..' км.')
				imgui.EndChild()
				imgui.SameLine()
				imgui.BeginChild("234", imgui.ImVec2(220, 40), true)
				imgui.Image(fuellogoff, imgui.ImVec2((24),24))
				imgui.SameLine()
				imgui.Image(enginelogoff, imgui.ImVec2((24),24))
						imgui.SameLine()
				imgui.Image(beltlogoff, imgui.ImVec2((24),24))
						imgui.SameLine()
				imgui.Image(repairlogoff, imgui.ImVec2((24),24))
						imgui.SameLine()
				imgui.Image(lightlogoff, imgui.ImVec2((24),24))
										imgui.SameLine()
				imgui.Image(speedlogoff, imgui.ImVec2((24),24))
														imgui.SameLine()
				imgui.Image(keylogoff, imgui.ImVec2((24),24))
				imgui.EndChild()
								imgui.SameLine()
				imgui.BeginChild("2354", imgui.ImVec2(270, 40), true)
								if imgui.Button(u8'A',imgui.ImVec2(40, 24)) then
				sampSendChat('/en')
				end
				imgui.SameLine()
				if imgui.Button(u8'B',imgui.ImVec2(40, 24)) then
				sampSendChat('/light')
				end
				imgui.SameLine()
				imgui.Button(u8'N',imgui.ImVec2(40, 24))
				imgui.SameLine()
				imgui.Button(u8'N',imgui.ImVec2(40, 24))
								imgui.SameLine()
				imgui.Button(u8'N',imgui.ImVec2(40, 24))
								imgui.SameLine()
				imgui.Button(u8'N',imgui.ImVec2(30, 24))			
				imgui.EndChild()
		end
				imgui.SameLine()
			imgui.End()                 -- Объявляем конец данного окна
    end
)
end
end

function fastmenu()
  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 1015
  local window_height = 633
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2


if openfastmenu and not framesfmenu_created then
framesfmenu_created = true
local newFrame = imgui.OnFrame(
function() return openfastmenu end,
function(player)                  
    local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
				imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100)                             
               
        imgui.Begin("Быстрое меню/Управление",window_openfastmenu, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)  -- Создаём новое окно с заголовком 'Main Window'
				  	imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant -> Биндер на время")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Панель управления SA-Assistant -> Биндер на время")) 
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant")).x) / 2)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 60) / 2); 
		imgui.Image(minlogo, imgui.ImVec2(60, 60))
		imgui.Separator()
		imgui.Spacing() 
imgui.BeginTitleChild(u8"Кнопки быстрого доступа", imgui.ImVec2(250, 385), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.Spacing()
if not flag then -- если цикл еще не был выполнен
    for i = 1, 9 do -- цикл от 1 до 10 с шагом 1
       table.insert(fmenu_settings, {name = "Свободный слот"..' '..'#'..#fmenu_settings, delay = 1000, text = "Ваш текст"})
       if i == 9 then -- если i равно 10
         flag = true -- устанавли
         break -- останавливаем цикл
        end
    end
end
        	 
for i = 1, 10 do	
		if imgui.Button(u8(fmenu_settings[i+1].name), imgui.ImVec2(-1, 30)) then
		fm = i
        end
end	
		 imgui.PopStyleColor(2)
imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))		 
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Настройки:", imgui.ImVec2(400, 385), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
	      local statusf_ptr = ffi.new("bool[1]", fmenu_settings[1].statusf.value)
if fmenu_settings[1].statusf.value then 
nameftog = 'Быстрое меню активировано'
statusonf = 'ON'
else
nameftog = 'Быстрое меню отключено'
statusonf = 'OFF'
end
					imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.085, 0.7, 0.085, 1.0))
			imgui.PushStyleColor (imgui.Col.TextDisabled	, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))	
	if ad.ToggleButton(u8(nameftog), statusf_ptr) then
	fmenu_settings[1].statusf.value = not fmenu_settings[1].statusf.value
	saveData(fmenu_settings, "moonloader/logo/fmenu.json")
		    if fmenu_settings[1].statusf.value then
	        hotkeyz = rkeys.registerHotKey(fmenu_settings[1].key, true, fastmainmenu)
		else
		hotkeyz = rkeys.unRegisterHotKey(fmenu_settings[1].key)
	    end
	end
				 imgui.PopStyleColor(2)
		imgui.Spacing()
				imgui.TextColored({1.0, 1, 1, 1.0}, u8("Выберите клавишу:")) 
		imgui.SameLine()
				fmenutable = {
			v = {fmenu_settings[1].key[1],fmenu_settings[1].key[2]}
		}
 if mimgui_addons.HotKey("##active2", fmenutable, 100) then
			fmenu_settings[1].key[1] = fmenutable.v[1]
			fmenu_settings[1].key[2] = fmenutable.v[2]
				fmenutable = {
					v = {fmenutable.v[1], fmenutable.v[2]}
				}
				saveData(fmenu_settings, "moonloader/logo/fmenu.json")
end
		 imgui.Spacing()
		 imgui.Separator()
		----
		imgui.Spacing()
							imgui.PushItemWidth(120)	
						imgui.TextColored({1.0, 1, 1, 1.0}, u8("Введите название для слота № 1"))
		imgui.SameLine()
ffi.copy(input_fmenuname_buf, u8(fmenu_settings[fm+1].name))

if imgui.InputText("##120", input_fmenuname_buf, ffi.sizeof(input_fmenuname_buf)) then
  fmenu_settings[fm+1].name = ffi.string(input_fmenuname_buf)
  			fmenu_settings[fm+1].name = u8:decode(fmenu_settings[fm+1].name)
		  if string.len(fmenu_settings[fm+1].name) < 1 then
  fmenu_settings[fm+1].name = 'Свободный слот '
  end
  saveData(fmenu_settings, "moonloader/logo/fmenu.json")
  end
-----------------
		 imgui.TextColored({1.0, 1, 1, 1.0}, u8("Установите задержку между строками:"))
		 local valuefmenu_ptr = ffi.new("int[1]", fmenu_settings[fm+1].delay)  -- указатель на текущее значение
		 		 imgui.SameLine()
local valuefmenu_ptr = ffi.new("int[1]", fmenu_settings[fm+1].delay)  -- указатель на текущее значение
if imgui.InputInt(u8("с"), valuefmenu_ptr, 1000,1000) then
  if valuefmenu_ptr[0] < 1000 then
    valuefmenu_ptr[0] = 1000
  end
  fmenu_settings[fm+1].delay = valuefmenu_ptr[0]  -- обновляем текущее значение
end
		  imgui.TextColored({1.0, 1, 1, 1.0}, u8("Дополнительные настройки: "))
			 imgui.NewLine()
			 imgui.Spacing()
			 		imgui.BeginTitleChild(u8"Примечание:##8", imgui.ImVec2(370, 133), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
imgui.Text(u8('* Длинна символов в названии в диапазоне 1-15\n* Значение задержки, 1 секунда = 1000'))
imgui.EndChild()
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Текст:", imgui.ImVec2(335, 385), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
ffi.copy(input_fmenutext_buf, u8(fmenu_settings[fm+1].text))
if imgui.InputTextMultiline(u8 "##138", input_fmenutext_buf, ffi.sizeof(input_fmenutext_buf),imgui.ImVec2(320, 340)) then
  fmenu_settings[fm+1].text = ffi.string(input_fmenutext_buf)
  			fmenu_settings[fm+1].text = u8:decode(fmenu_settings[fm+1].text)
end
imgui.EndChild()

		if imgui.Button(u8("Сохранить"), imgui.ImVec2(-1, 30)) then
saveData(fmenu_settings, "moonloader/logo/fmenu.json")
        end
        if imgui.Button(u8("Удалить"), imgui.ImVec2(-1, 30)) then
end
        if imgui.Button(u8("Вернуть назад"), imgui.ImVec2(-1, 30)) then
        openfastmenu = false
		open = true
        end
        if imgui.Button(u8("Закрыть"), imgui.ImVec2(-1, 30)) then
         openfastmenu = false
        end
        imgui.End()                 -- Объявляем конец данного окна
    end
)
end
		 imgui.PopStyleColor(2) 
end

function timeBindCommand()
  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 1015
  local window_height = 633
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2


if opentime and not framestime_created then
framestime_created = true
local newFrame = imgui.OnFrame(
function() return opentime end,
function(player)        
    local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100)
        imgui.Begin("БиндВремя",window_opentime, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)  -- Создаём новое окно с заголовком 'Main Window'
	  	imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant -> Биндер на время")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Панель управления SA-Assistant -> Биндер на время")) 
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant")).x) / 2)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 60) / 2); 
		imgui.Image(minlogo, imgui.ImVec2(60, 60))
		imgui.Separator()
		imgui.Spacing() 
        imgui.BeginTitleChild(u8"Назначенное время:", imgui.ImVec2(250, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		if imgui.Button(u8("Создать слот"), imgui.ImVec2(-1, 30)) then
		    table.insert(time_setting, {time =  "18:00:00", name = "Свободный слот "..#time_setting + 1, delay = 1000, text = "Ваш текст", statustime = {value = false}})
        saveData(time_setting, "moonloader/logo/timelist.json")
		end
		imgui.Separator()
		    imgui.BeginChild("Список времен", imgui.ImVec2(230, 300), true)
			for k, v in pairs(time_setting) do
			if time_setting[k].statustime.value then 
statusontime = '[ON]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
else
statusontime = '[OFF]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
end
			if  imgui.Button(u8(time_setting[k].name)..'  '..statusontime, imgui.ImVec2(-1, 30)) then
			 	zom = k
			 end
			    imgui.PopStyleColor(2)
					    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
			end
			imgui.EndChild()
        imgui.EndChild()
		imgui.SameLine()
		imgui.BeginTitleChild(u8"Настройки:##3", imgui.ImVec2(400, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
        local statustime_ptr = ffi.new("bool[1]", time_setting[zom].statustime.value)
if time_setting[zom].statustime.value then 
nametimetog = 'Бинд активен'
statusontime = 'ON'
else
nametimetog = 'Бинд неактивен'
statusontime = 'OFF'
end
					imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.085, 0.7, 0.085, 1.0))
			imgui.PushStyleColor (imgui.Col.TextDisabled	, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))	
		if ad.ToggleButton(u8(nametimetog), statustime_ptr) then
		 time_setting[zom].statustime.value = not time_setting[zom].statustime.value
		 saveData(time_setting, "moonloader/logo/timelist.json")
		end
		 imgui.PopStyleColor(2)
 	 imgui.Spacing()
					imgui.BeginChild("settingtime", imgui.ImVec2(400, 310), true)
					imgui.PushItemWidth(120)	
						imgui.TextColored({1.0, 1, 1, 1.0}, u8("Введите название для Вашей таймера: "))
		imgui.SameLine()
ffi.copy(input_timename_buf, u8(time_setting[zom].name))

if imgui.InputText("##100", input_timename_buf, ffi.sizeof(input_timename_buf)) then
  time_setting[zom].name = ffi.string(input_timename_buf)
  			time_setting[zom].name = u8:decode(time_setting[zom].name)
			  if string.len(time_setting[zom].name) < 1 then
  time_setting[zom].name = 'Свободный слот '..zom
  saveData(time_setting, "moonloader/logo/timelist.json")
  end
  end
 imgui.TextColored({1.0, 1, 1, 1.0}, u8("Установите время: "))
 		imgui.SameLine()
		imgui.PushItemWidth(80)	
ffi.copy(input_time_buf, u8(time_setting[zom].time))

if imgui.InputText("##101", input_time_buf, ffi.sizeof(input_time_buf)) then
  time_setting[zom].time = ffi.string(input_time_buf)
  			time_setting[zom].time = u8:decode(time_setting[zom].time)
			  if string.len(time_setting[zom].time) < 1 then
  time_setting[zom].time = '18:00:00'
  saveData(time_setting, "moonloader/logo/timelist.json")
  end
  end
		 imgui.PopStyleColor(2)
       imgui.PushItemWidth(110)			 
		  imgui.TextColored({1.0, 1, 1, 1.0}, u8("Установите задержку между строками:"))
		 local valuetime_ptr = ffi.new("int[1]", time_setting[zom].delay)  -- указатель на текущее значение
		 		 imgui.SameLine()
local valuetime_ptr = ffi.new("int[1]", time_setting[zom].delay)  -- указатель на текущее значение
if imgui.InputInt(u8("с"), valuetime_ptr, 1000,1000) then
  if valuetime_ptr[0] < 1000 then
    valuetime_ptr[0] = 1000
  end
  time_setting[zom].delay = valuetime_ptr[0]  -- обновляем текущее значение
  saveData(time_setting, "moonloader/logo/timelist.json")
end
		     imgui.TextColored({1.0, 1, 1, 1.0}, u8("Дополнительные настройки: "))
			 imgui.NewLine()
			 imgui.Spacing()
			 		imgui.BeginTitleChild(u8"Примечание:##3", imgui.ImVec2(370, 133), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
imgui.Text(u8('* Длинна символов в названии в диапазоне 1-15 \n* Формат времени должен соответстовать: 00:00:00\n* Значение задержки, 1 секунда = 1000\n* Активация сбрасывается после отработки бинда.\n* Последний слот удалить - нельзя'))
imgui.EndChild()
if logwarning then
imgui.TextColored({0.7, 0.085, 0.085, 1.0}, u8("[Ошибка#1]: Невозможно удалить данный слот."))
timer = timer or 0
timer = timer + imgui.GetIO().DeltaTime
if timer > 2 then
logwarning = false
timer = 0
end
end
				imgui.EndChild()
		imgui.EndChild()
		imgui.SameLine()
		imgui.BeginTitleChild(u8"Ваш текст:##5", imgui.ImVec2(335, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
ffi.copy(input_timetext_buf, u8(time_setting[zom].text))
if imgui.InputTextMultiline(u8 "##137", input_timetext_buf, ffi.sizeof(input_timetext_buf),imgui.ImVec2(320, 345)) then
  time_setting[zom].text = ffi.string(input_timetext_buf)
  			time_setting[zom].text = u8:decode(time_setting[zom].text)
			saveData(time_setting, "moonloader/logo/timelist.json")
end
		imgui.EndChild()
		if imgui.Button(u8("Сохранить"), imgui.ImVec2(-1, 30)) then
saveData(time_setting, "moonloader/logo/timelist.json")
        end
        if imgui.Button(u8("Удалить"), imgui.ImVec2(-1, 30)) then
if time_setting[zom] == time_setting[#time_setting]then
logwarning = true
else
table.remove(time_setting, zom)
saveData(time_setting, "moonloader/logo/timelist.json")
end
        end
        if imgui.Button(u8("Вернуть назад"), imgui.ImVec2(-1, 30)) then
       opentime = false
        open = true
        end
        if imgui.Button(u8("Закрыть"), imgui.ImVec2(-1, 30)) then
         opentime = false
        end
		 imgui.PopStyleColor(2) 
	   imgui.End()            
    end
)
end
end



function mainBindCommand()

  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 1015
  local window_height = 633
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2


if menucommand and not framescommand_created then
framescommand_created = true
local newFrame = imgui.OnFrame(
function() return menucommand end,
function(player)        
    local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
	  	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100)
        imgui.Begin("Бинд коммандой",window_menucommand, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)  -- Создаём новое окно с заголовком 'Main Window'
	  	imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant -> Биндер на команду")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Панель управления SA-Assistant -> Биндер на команду")) 
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant")).x) / 2)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 60) / 2); 
		imgui.Image(minlogo, imgui.ImVec2(60, 60))
		imgui.Separator()
		imgui.Spacing() 
        imgui.BeginTitleChild(u8"Список комманд: ", imgui.ImVec2(250, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		if imgui.Button(u8("Создать команду"), imgui.ImVec2(-1, 30)) then
		    table.insert(command_setting, {command =  "example", name = "Свободный слот "..#command_setting + 1, delay = 1000, text = "Ваш текст", statuscommand = {value = false}})
       saveData(command_setting, "moonloader/logo/comlist.json")
	   end
		imgui.Separator()
		    imgui.BeginChild("Список комманд", imgui.ImVec2(230, 300), true)
			for k, v in pairs(command_setting) do
			if command_setting[k].statuscommand.value then 
statusoncom = '[ON]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
	imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
else
statusoncom = '[OFF]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
end
			if  imgui.Button(u8(command_setting[k].name)..'  '..statusoncom, imgui.ImVec2(-1, 30)) then
			 	ccom = k
			 end
			    imgui.PopStyleColor(2)
			end
			 imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
			imgui.EndChild()
        imgui.EndChild()
		imgui.SameLine()
		imgui.BeginTitleChild(u8"Настройки:##2", imgui.ImVec2(400, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
        local statuscommand_ptr = ffi.new("bool[1]", command_setting[ccom].statuscommand.value)
if command_setting[ccom].statuscommand.value then 
namecomtog = 'Бинд активен'
statusoncom = 'ON'
else
namecomtog = 'Бинд неактивен'
statusoncom = 'OFF'
end
				imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.085, 0.7, 0.085, 1.0))
			imgui.PushStyleColor (imgui.Col.TextDisabled	, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))	
		if ad.ToggleButton(u8(namecomtog), statuscommand_ptr) then
		 command_setting[ccom].statuscommand.value = not command_setting[ccom].statuscommand.value
		 registerCommands()
		 saveData(command_setting, "moonloader/logo/comlist.json")
		end
				 imgui.PopStyleColor(2)
 	 imgui.Spacing()
					imgui.BeginChild("settingcommand", imgui.ImVec2(400, 310), true)
					imgui.PushItemWidth(120)	
						imgui.TextColored({1.0, 1, 1, 1.0}, u8("Введите название для Вашей команды: "))
		imgui.SameLine()
ffi.copy(input_com_buf, u8(command_setting[ccom].name))

if imgui.InputText("##5", input_com_buf, ffi.sizeof(input_com_buf)) then
  command_setting[ccom].name = ffi.string(input_com_buf)
  			command_setting[ccom].name = u8:decode(command_setting[ccom].name)
			  if string.len(command_setting[ccom].name) < 1 then
  command_setting[ccom].name = 'Свободный слот '..ccom
  saveData(command_setting, "moonloader/logo/comlist.json")
  end
  end
 imgui.TextColored({1.0, 1, 1, 1.0}, u8("Введите команду: "))
 		imgui.SameLine()
		imgui.PushItemWidth(80)	
ffi.copy(input_maincom_buf, u8(command_setting[ccom].command))

if imgui.InputText("##6", input_maincom_buf, ffi.sizeof(input_maincom_buf)) then
  command_setting[ccom].command = ffi.string(input_maincom_buf)
  			command_setting[ccom].command = u8:decode(command_setting[ccom].command)
			  if string.len(command_setting[ccom].command) < 1 then
  command_setting[ccom].command = '/exampe'..ccom
  saveData(command_setting, "moonloader/logo/comlist.json")
  end
  end
		 imgui.PopStyleColor(2)
       imgui.PushItemWidth(110)			 
		  imgui.TextColored({1.0, 1, 1, 1.0}, u8("Установите задержку между строками:"))
		 local value_ptr = ffi.new("int[1]", button_settings[y].delay)  -- указатель на текущее значение
		 		 imgui.SameLine()
local valuecom_ptr = ffi.new("int[1]", command_setting[ccom].delay)  -- указатель на текущее значение
if imgui.InputInt(u8("с"), valuecom_ptr, 1000,1000) then
  if valuecom_ptr[0] < 1000 then
    valuecom_ptr[0] = 1000
  end
  command_setting[ccom].delay = valuecom_ptr[0]  -- обновляем текущее значение
  saveData(command_setting, "moonloader/logo/comlist.json")
end
		     imgui.TextColored({1.0, 1, 1, 1.0}, u8("Дополнительные настройки: "))
			 imgui.NewLine()
			 imgui.Spacing()
			 		imgui.BeginTitleChild(u8"Примечание:##2", imgui.ImVec2(370, 133), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
					imgui.Text(u8('* Длинна символов в названии в диапазоне 1-15 \n* Формат команды должен быть без знака "/"\n* Значение задержки, 1 секунда = 1000\n* Последний слот удалить - нельзя'))
imgui.EndChild()
if logwarning then
imgui.TextColored({0.7, 0.085, 0.085, 1.0}, u8("[Ошибка#1]: Невозможно удалить данный слот."))
timer = timer or 0
timer = timer + imgui.GetIO().DeltaTime
if timer > 2 then
logwarning = false
timer = 0
end
end
				imgui.EndChild()
		imgui.EndChild()
		imgui.SameLine()
		imgui.BeginTitleChild(u8"Ваш текст:##3", imgui.ImVec2(335, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
ffi.copy(input_maincomtext_buf, u8(command_setting[ccom].text))
if imgui.InputTextMultiline(u8 "##135", input_maincomtext_buf, ffi.sizeof(input_maincomtext_buf),imgui.ImVec2(320, 345)) then
  command_setting[ccom].text = ffi.string(input_maincomtext_buf)
  			command_setting[ccom].text = u8:decode(command_setting[ccom].text)
			saveData(command_setting, "moonloader/logo/comlist.json")
end
		imgui.EndChild()
		if imgui.Button(u8("Зарегистрировать команду"), imgui.ImVec2(-1, 30)) then
		saveData(command_setting, "moonloader/logo/comlist.json")
        end
        if imgui.Button(u8("Удалить команду"), imgui.ImVec2(-1, 30)) then
if command_setting[ccom] == command_setting[#command_setting]then
logwarning = true
else
table.remove(command_setting, ccom)
saveData(command_setting, "moonloader/logo/comlist.json")
end
        end
        if imgui.Button(u8("Вернуть назад"), imgui.ImVec2(-1, 30)) then
        menucommand = false
        open = true
        end
        if imgui.Button(u8("Закрыть"), imgui.ImVec2(-1, 30)) then
        menucommand = false
        end
		 imgui.PopStyleColor(2) 
	   imgui.End()            
    end
)
end
end

function mainBindKey()

  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 1015
  local window_height = 633
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2

if openbindmenukey and not frameskey_created then
frameskey_created = true
local newFrame = imgui.OnFrame(
function() return openbindmenukey end,
function(player)
    local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
	  	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
		--	imgui.PushStyleColor (imgui.Col.Separator, imgui.ImVec4(0.10, 1, 0.12, 1.00)) -- test
      imgui.SetNextWindowBgAlpha(100)
      imgui.Begin("Бинд клавиш",window_openbindmenukey, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
	  		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant -> Биндер на клавишу")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Панель управления SA-Assistant -> Биндер на клавишу")) 
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant")).x) / 2)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 60) / 2); 
		imgui.Image(minlogo, imgui.ImVec2(60, 60))
		imgui.Separator()
		imgui.Spacing()
		imgui.BeginTitleChild(u8"Бинды:", imgui.ImVec2(250, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
  if imgui.Button(u8("Создать клавишу"), imgui.ImVec2(-1, 30)) then
    table.insert(button_settings, {name = "Свободный слот "..#button_settings + 1, key = {nil,nil}, delay = 1000, text = "Ваш текст", statusbutton = {value = false}})
  saveData(button_settings, "moonloader/logo/buttonlist.json")
  end

imgui.Separator()
local z = 1
		imgui.BeginChild("AAРация", imgui.ImVec2(230, 300), true)
for k, v in pairs(button_settings) do
if button_settings[k].statusbutton.value then 
statuson = '[ON]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
else
statuson = '[OFF]'
imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
end
if imgui.Button(u8(button_settings[k].name..' '..statuson), imgui.ImVec2(-1, 30)) then
enasos = not enasos
y = k
end
   imgui.PopStyleColor(2)  
   	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
end
	imgui.EndChild()
imgui.EndChild()
imgui.SameLine()
		imgui.BeginTitleChild(u8"Настройки:", imgui.ImVec2(400, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
local statusbutton_ptr = ffi.new("bool[1]", button_settings[y].statusbutton.value)
if button_settings[y].statusbutton.value then 
nametoggle = 'Бинд активен'
statuson = 'ON'
else
nametoggle = 'Бинд неактивен'
statuson = 'OFF'
end
				imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.085, 0.7, 0.085, 1.0))
			imgui.PushStyleColor (imgui.Col.TextDisabled	, imgui.ImVec4(0.7, 0.085, 0.085, 1.0))
if ad.ToggleButton(u8(nametoggle), statusbutton_ptr) then
  button_settings[y].statusbutton.value = not button_settings[y].statusbutton.value
  keyactivate()
  saveData(button_settings, "moonloader/logo/buttonlist.json")
end			
 imgui.PopStyleColor(2) 
					imgui.BeginChild("AAРация2", imgui.ImVec2(400, 310), true)	
imgui.PushItemWidth(130)					
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Введите название для вашего бинда: "))
		imgui.SameLine()
ffi.copy(input_text_buf, u8(button_settings[y].name))

if imgui.InputText("", input_text_buf, ffi.sizeof(input_text_buf)) then
  button_settings[y].name = ffi.string(input_text_buf)
  			button_settings[y].name = u8:decode(button_settings[y].name)
			  if string.len(button_settings[y].name) < 1 then
  button_settings[y].name = 'Свободный слот '..y
  saveData(button_settings, "moonloader/logo/buttonlist.json")
  end
end
imgui.PopItemWidth()

		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Выберите клавишу:")) 
		imgui.SameLine()
				johnykey = {
			v = {button_settings[y].key[1],button_settings[y].key[2]}
		}
if mimgui_addons.HotKey("##active", johnykey, 100) then
			button_settings[y].key[1] = johnykey.v[1]
			button_settings[y].key[2] = johnykey.v[2]
				johnykey[y] = {
					v = {johnykey.v[1], johnykey.v[2]}
				}
				saveData(button_settings, "moonloader/logo/buttonlist.json")
end
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Установите задержку между строками:")) 
				imgui.SameLine()
				imgui.PushItemWidth(110)
local value_ptr = ffi.new("int[1]", button_settings[y].delay)  -- указатель на текущее значение
if imgui.InputInt(u8("с"), value_ptr, 1000,1000) then
  if value_ptr[0] < 1000 then
    value_ptr[0] = 1000
  end
  button_settings[y].delay = value_ptr[0]  -- обновляем текущее значение
  saveData(button_settings, "moonloader/logo/buttonlist.json")
end
imgui.PopItemWidth()
		imgui.Spacing()
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Долполнительные настройки:")) 
		imgui.NewLine()
			--ad.ToggleButton(u8('Активировать отправку в чат -- Разработка'), toggle1)
				--		if toggle1[0] then
				--	imgui.Text(u8('Текст сразу будет отправляться в чат, после активацией.\n'))
			--	end
		imgui.BeginTitleChild(u8"Примечание:", imgui.ImVec2(370, 153), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
							imgui.Text(u8('* Длинна символов в названии в диапазоне 1-15\n* Значение задержки, 1 секунда = 1000\n* Последний слот удалить - нельзя'))
imgui.EndChild()
if logwarning then
imgui.TextColored({0.7, 0.085, 0.085, 1.0}, u8("[Ошибка#1]: Невозможно удалить данный слот."))
timer = timer or 0
timer = timer + imgui.GetIO().DeltaTime
if timer > 2 then
logwarning = false
timer = 0
end
end
imgui.EndChild()
imgui.EndChild()
imgui.SameLine()
		imgui.BeginTitleChild(u8"Ваш текст:", imgui.ImVec2(335, 390), imgui.ImVec4(0.10, 0.09, 0.12, 1.00), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
ffi.copy(input_mtext_buf, u8(button_settings[y].text))
if imgui.InputTextMultiline(u8 "##135", input_mtext_buf, ffi.sizeof(input_mtext_buf),imgui.ImVec2(320, 345)) then
  button_settings[y].text = ffi.string(input_mtext_buf)
  			button_settings[y].text = u8:decode(button_settings[y].text)
			saveData(button_settings, "moonloader/logo/buttonlist.json")
end
imgui.EndChild()
      imgui.PopStyleColor(2)  
	  		imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0.12, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
if imgui.Button(u8("Сохранить"), imgui.ImVec2(-1, 30)) then
saveData(button_settings, "moonloader/logo/buttonlist.json")
end
if imgui.Button(u8("Удалить"), imgui.ImVec2(-1, 30)) then

if button_settings[y] == button_settings[#button_settings]then
logwarning = true
else
table.remove(button_settings, y)
saveData(button_settings, "moonloader/logo/buttonlist.json")
end
end
if imgui.Button(u8("Вернуть назад"), imgui.ImVec2(-1, 30)) then
openbindmenukey = false
open = true
end
if imgui.Button(u8("Закрыть"), imgui.ImVec2(-1, 30)) then
openbindmenukey = false
end
	  imgui.End()
	        imgui.PopStyleColor(2)  
    end
)
end
end


function openNewPanel()

  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 1015
  local window_height = 633
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2

if open and not frames_created then
frames_created = true
local newFrame = imgui.OnFrame(
function() return open end,
function(player)
      local window_size = imgui.ImVec2(window_width, window_height)
      imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
      local window_pos = imgui.ImVec2(window_x, window_y)
      imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)
      imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
	  	  	imgui.PushStyleColor (imgui.Col.ButtonActive, imgui.ImVec4(0.0, 0.0, 0.0, 1.0))
      imgui.PushStyleColor(imgui.Col.WindowBg, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
	  	    imgui.PushStyleColor (imgui.Col.FrameBg, imgui.ImVec4(0.10, 0.09, 0.12, 1.00))
      imgui.SetNextWindowBgAlpha(100)
      imgui.Begin("Новая панель управления", window_open_pan, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Панель управления SA-Assistant")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Панель управления SA-Assistant"))        -- Добавляем туда текст 'Hello'
		imgui.Separator()
		imgui.Spacing()
		imgui.BeginChild("AA", imgui.ImVec2(1000, 167), true)
imgui.BeginTitleChild(u8"Биндер на клавишу", imgui.ImVec2(190, 150), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 90) / 2); 
		imgui.Image(logokey, imgui.ImVec2(81, 73))
if imgui.Button(u8("Выбрать"), imgui.ImVec2(-1, 30)) then
open = false
openbindmenukey = true
mainBindKey()
end
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Биндер на команду", imgui.ImVec2(190, 150), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 90) / 2); 
		imgui.Image(logocom, imgui.ImVec2(81, 73))
if imgui.Button(u8("Выбрать"), imgui.ImVec2(-1, 30)) then
open = false
menucommand = true
mainBindCommand()
end
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Биндер на время", imgui.ImVec2(190, 150), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 90) / 2); 
		imgui.Image(logotime, imgui.ImVec2(81, 73))
if imgui.Button(u8("Выбрать"), imgui.ImVec2(-1, 30)) then
open = false
opentime = true
timeBindCommand()
end
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Быстрое меню", imgui.ImVec2(190, 150), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 90) / 2); 
		imgui.Image(logonot, imgui.ImVec2(81, 73))
if imgui.Button(u8("Выбрать"), imgui.ImVec2(-1, 30)) then
open = false
openfastmenu = true
fastmenu()
end
imgui.EndChild()
imgui.SameLine()
imgui.BeginTitleChild(u8"Круговое меню", imgui.ImVec2(190, 150), imgui.ImVec4(0.7, 0.085, 0.085, 1.0), imgui.ImVec4(1.00, 1.00, 1.00, 1.00), imgui.ImVec4(0.12, 0.12, 0.12, 1.0), 40)
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 90) / 2); 
		imgui.Image(logonot, imgui.ImVec2(81, 73))
if imgui.Button(u8("Выбрать"), imgui.ImVec2(-1, 30)) then
end
imgui.EndChild()
imgui.EndChild()
--[[
		imgui.BeginChild("Прочие настройки##1", imgui.ImVec2(1000, 167), true)
		 imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(2, 2, 2, 2))
				imgui.BeginChild("Прочие настройки##2", imgui.ImVec2(190, 85), true)
					  		imgui.SetCursorPosX((190 - imgui.CalcTextSize(u8("Меню транспорта")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Меню транспорта")) 
		imgui.NewLine()
					ad.ToggleButton(u8('Спидометр'), toggle)
									if toggle[0] then
					       if isCharInAnyCar(PLAYER_PED) then
						   speedometr()
	   sampTextdrawDelete(12)
	   sampTextdrawDelete(10)
	   sampTextdrawDelete(13)
	   sampTextdrawDelete(20)
                    fuel = sampTextdrawGetString(fuel_textdraw_id)
                    fuel_l = tonumber(fuel)
					mile = sampTextdrawGetString(mile_textdraw_id)
                    mile_l = tonumber(mile)
      local carhandle = storeCarCharIsInNoSave(PLAYER_PED) -- Получения handle транспорта
      idcar = getCarModel(carhandle) -- Получение ID транспорта
	   health = getCarHealth(storeCarCharIsInNoSave(PLAYER_PED))
	  namecar = getNameOfVehicleModel(idcar)
            speed = getCarSpeed(storeCarCharIsInNoSave(PLAYER_PED))
            speed = speed * 3.2
		else 
       end
				end
imgui.EndChild()
imgui.SameLine()
				imgui.BeginChild("Прочие настройки##3", imgui.ImVec2(190, 85), true)
					  		imgui.SetCursorPosX((190 - imgui.CalcTextSize(u8("Меню транспорта")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Настройки стрельбы")) 
		imgui.NewLine()
					ad.ToggleButton(u8('Трейсер Пуль'), toggle1)
imgui.EndChild()
imgui.SameLine()
				imgui.BeginChild("Прочие настройки##4", imgui.ImVec2(290, 85), true)
					  		imgui.SetCursorPosX((290 - imgui.CalcTextSize(u8("Меню транспорта")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Настройки онлайна")) 
		imgui.Spacing()
					ad.ToggleButton(u8('Работа в свернутом режиме'), toggle2)
					ad.ToggleButton(u8('Прокачка онлайна(АнтиАФК)'), toggle3)
imgui.EndChild()
imgui.EndChild()
]]
imgui.PopStyleColor(1)


		imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0.15, 0.15, 0.15, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
	--	imgui.SetCursorPosX((imgui.GetWindowWidth() - 300) / 2); 
		--imgui.Image(biglogo, imgui.ImVec2(300, 305))
		imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Разработка...")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Разработка..."))        -- Добавляем туда текст 'Hello'
		imgui.NewLine()
		imgui.Spacing()
		         if imgui.Checkbox("Checkbox2", ffi.new("bool[1]", CheckBoxWorkOff)) then
				 CheckBoxWorkOff = not CheckBoxWorkOff
				 	if CheckBoxWorkOff then
		memory.write(0x747FB6, 0x1, 1, true)
		memory.write(0x74805A, 0x1, 1, true)
		memory.fill(0x74542B, 0x90, 8, true)
		memory.fill(0x53EA88, 0x90, 6, true)
		sampAddChatMessage('Работа в свёрнутом режиме включена', -1)
	else
		memory.write(0x747FB6, 0x0, 1, true)
		memory.write(0x74805A, 0x0, 1, true)
		arr = { 0x50, 0x51, 0xFF, 0x15, 0x00, 0x83, 0x85, 0x00 }
		memset(0x74542B)
		arr = { 0x0F, 0x84, 0x7B, 0x01, 0x00, 0x00 }
		memset(0x53EA88)
		sampAddChatMessage('Работа в свёрнутом режиме отключена', -1)
	end
				 end
		        if imgui.Checkbox("Checkbox", ffi.new("bool[1]", checkbox_state)) then
				checkbox_state = not checkbox_state
        end
imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(2, 2, 2, 2))
		imgui.BeginChild("AA3", imgui.ImVec2(410, 250), true)
				        if imgui.Checkbox(u8("Активировать/Деактивировать"), ffi.new("bool[1]", CheckBoxWorkNews)) then
				CheckBoxWorkNews = not CheckBoxWorkNews
        end
		 imgui.SliderInt(u8("Время показа линий"), BulletTime, 1, 40)
		 imgui.SliderInt(u8("Толщина линий"), BulletWH, 1, 40)
		 		if imgui.ColorEdit4(u8("Цвет поподания в объект"), BulletColorObject) then
				local r, g, b, a = imgui.ImColor(BulletColorObject):GetRGBA()
		end
		imgui.EndChild()
		 imgui.PopStyleColor()
 if imgui.Button(u8("Вернуться в главное меню"), imgui.ImVec2(-1, 30)) then
 open = false
 window_open = true
end
if imgui.Button(u8("Закрыть"), imgui.ImVec2(-1, 30)) then
open = false
end
      -- Здесь вы можете добавить ваш интерфейс

      imgui.End()
      imgui.PopStyleColor(2)               -- Объявляем конец данного окна
    end
)
end
end


function openControlPanel()
window_open = not window_open
window_open_ptr[0] = window_open
if window_open and not frame_created then
frame_created = true
local newFrame = imgui.OnFrame(
function() return window_open end,
function(player)
 -- Определяем размеры и позицию интерфейса
  local screenWidth, screenHeight = getScreenResolution()
  local window_width = 400
  local window_height = 543
  local window_x = (screenWidth - window_width) / 2
  local window_y = (screenHeight - window_height) / 2

  -- Создаём окно с интерфейсом
  local window_size = imgui.ImVec2(window_width, window_height)
  imgui.SetNextWindowSize(window_size, imgui.Cond.FirstUseEver)
  local window_pos = imgui.ImVec2(window_x, window_y)
  imgui.SetNextWindowPos(window_pos, imgui.Cond.FirstUseEver)  
     imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0.06, 0.05, 0.07, 0.00))
	 imgui.PushStyleColor(imgui.Col.WindowBg,imgui.ImVec4(0.07450980392, 0.07450980392, 0.07450980392, 1.0))
     imgui.SetNextWindowBgAlpha(100)
        imgui.Begin("SA-Assistant Control Panel", window_open_ptr, imgui.WindowFlags.NoResize + imgui.WindowFlags.NoMove + imgui.WindowFlags.NoCollapse + imgui.WindowFlags.NoTitleBar)
imgui.PushFont(big)
	   imgui.SetCursorPosX((window_width - imgui.CalcTextSize("SA-Assistant Control Panel v.0.0.11").x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, "SA-Assistant Control Panel v.0.0.11")        -- Добавляем туда текст 'Hello'
		imgui.PopFont()
		imgui.Spacing()
		imgui.Separator()
		imgui.Spacing()
			   imgui.SetCursorPosX((window_width - imgui.CalcTextSize(u8("Вас приветствует игровой помошник SA-Assistant")).x) / 2)
		imgui.TextColored({1.0, 1, 1, 1.0}, u8("Вас приветствует игровой помошник SA-Assistant"))        -- Добавляем туда текст 'Hello'
		imgui.NewLine()
		imgui.SetCursorPosX((imgui.GetWindowWidth() - 170) / 2); 
		imgui.Image(mainlogo, imgui.ImVec2(170, 170))
				imgui.NewLine()
		imgui.PushStyleColor(imgui.Col.ButtonHovered, imgui.ImVec4(0.12, 0.12, 0.12, 1.0))
		imgui.PushStyleColor(imgui.Col.Button, imgui.ImVec4(0.08, 0.08, 0.08, 1.0))
		imgui.PushStyleColor(imgui.Col.ButtonActive, imgui.ImVec4(0.25, 0.25, 0.25, 1.0))
local button_size = imgui.ImVec2(-1, 50)  -- размер кнопки (ширина 100, высота 50)
if imgui.Button(u8("Панель управления"), button_size) then
window_open = false
open = true
openNewPanel()
  -- тут код, который будет выполнен при нажатии на кнопку
end
if imgui.Button(u8("Тех.Поддержка"), button_size) then
end
if imgui.Button(u8("Обновление"), button_size) then
end
if imgui.Button(u8("Тех.Поддержка"), button_size) then

end
if imgui.Button(u8("Выйти"), button_size) then
window_open = false

end
 imgui.PopStyleColor()
        imgui.End()     
		-- Объявляем конец данного окна
    end
)
else
end
end
function main()
    repeat wait(0) until isSampAvailable()
sampAddChatMessage('{e84118}[SA-Assistant]{FFFFFF} Загружен и готов к работе. Для входа в панель управления введите /sap',0xFFFFFF)
sampRegisterChatCommand("sap", openControlPanel)
sampRegisterChatCommand("copmenu", maincopmenu)
sampRegisterChatCommand("clog", captlogmenu)
    sampRegisterChatCommand("flood", function() -- регистрируем команду /flood
        sampSendChat("/flood") -- отправляем текст команды в чат
    end)
    
    sampRegisterChatCommand("sync", function() -- регистрируем команду /sync
        local veh = sampGetCarHandleBySampPlayerId(sampGetPlayerId()) -- получаем хэндл автомобиля игрока
        local pos = {x = 0.0, y = 0.0, z = 0.0} -- задаем позицию для синхронизации
        local vel = {x = 0.0, y = 0.0, z = 0.0} -- задаем скорость для синхронизации
        local quat = {w = 0.0, x = 0.0, y = 0.0, z = 0.0} -- задаем кватернион для синхронизации
        sampSendVehicleSync(veh, pos, vel, quat) -- отправляем синхронизацию автомобиля
    end)
    sampRegisterChatCommand("rpat", function()
        radarAndHudPatch(true)
        sampAddChatMessage("Патч включён", -1)
    end)
logotype()
  -- Запускаем функцию keyactivate
    if doesFileExist("moonloader/logo/timelist.json") then
        local file = io.open('moonloader/logo/timelist.json', 'r')
        if file then
            time_setting = decodeJson(file:read('*a'))
        end
    end
	    if doesFileExist("moonloader/logo/buttonlist.json") then
        local file = io.open('moonloader/logo/buttonlist.json', 'r')
        if file then
            button_settings = decodeJson(file:read('*a'))
        end
    end
	    if doesFileExist("moonloader/logo/comlist.json") then
        local file = io.open('moonloader/logo/comlist.json', 'r')
        if file then
            command_setting = decodeJson(file:read('*a'))
        end
    end
		    if doesFileExist("moonloader/logo/fmenu.json") then
        local file = io.open('moonloader/logo/fmenu.json', 'r')
        if file then
            fmenu_settings = decodeJson(file:read('*a'))
        end
    end
	       colortext = u8(colortext)
	 for k,v in pairs(fmenu_settings) do
	 		    if fmenu_settings[1].statusf.value then
	        hotkeyz = rkeys.registerHotKey(fmenu_settings[1].key, true, fastmainmenu)
		else
		hotkeyz = rkeys.unRegisterHotKey(fmenu_settings[1].key)
	    end
	 end
	 keyactivate()
	 registerCommands()
  -- Бесконечный цикл, в котором постоянно обновляем функцию keyactivate
  while true do
  if checkbox_state then
	sampSendChat("/test1")
    wait(5000)
end
  if CheckBoxWorkNews then
		renderBulletTracer()
end
	 	lua_thread.create(function()
	  	timeregister()
function samp.onShowDialog(dialogId,s,t,b1,b2,text)
    for line in text:gmatch("[^\n]+") do -- разбиваем чтобы искать по строкам
        if line:find('Текст:') then -- Поиск текста в строке. |  Пример строки Текст: [some text]
            outputText = line:match('Текст:(.*)') or "0" -- запишет в переменную outputText текст после 'Текст:'
			outputText = string.sub(outputText, 10)
			outputText = u8(outputText)
            sampAddChatMessage('Текст объявления: '.. u8:decode(outputText), -1)-- выведет в чат значение переменной outputText
        end
     end
end
	wait(0)
	end)
        local weapon = getCurrentCharWeapon(PLAYER_PED) -- get the player's current weapon
        if weapon ~= 0 then -- if the player is holding a weapon
            if weapon == 24 then -- if the player is holding an M4
                while isCharShooting(PLAYER_PED) do -- while the player is shooting
                    local startTime = os.clock() -- record the start time
                    wait(0) -- wait a frame to prevent the script from freezing the game
                    local totalTime = getCharAnimTotalTime(PLAYER_PED, "M4_FIRE") -- get the total time for the M4_FIRE animation
                    local recoilTime = totalTime - (os.clock() - startTime) -- calculate the time it takes for the ped to return to its idle animation after firing
                    local recoil = recoilTime * 10 -- estimate the recoil as a multiple of 10 milliseconds
                    sampAddChatMessage("[M4 RECOIL] " .. recoil, -1) -- output the player's recoil to the SAMP chat
                end
            end
        else
        end
    wait(0)
  end
  
end
-- Все цвета указываются так: imgui.ImVec4(0.34, 0.36, 0.78, 1.00)
--colorBegin - Цвет заголовка
--colorText - Цвет текста в заголовке
--colorLine - Цвет обводки чилда
--offset - Смещение заголовка на "ваше число (int)" пикселей вправо

function imgui.BeginTitleChild(str_id, size, colorBegin, colorText, colorLine, offset)
    colorBegin = colorBegin or imgui.ImVec4(0.34, 0.36, 0.78, 1.00)
    colorText = colorText or imgui.ImVec4(1.00, 1.00, 1.00, 1.00)
    colorLine = colorLine or imgui.ImVec4(0.34, 0.36, 0.78, 1.00)
    offset = offset or 50
    local DL = imgui.GetWindowDrawList()
    local posS = imgui.GetCursorScreenPos()
    local rounding = imgui.GetStyle().ChildRounding
    local title = str_id:gsub('##.+$', '')
    local sizeT = imgui.CalcTextSize(title)
    local bgColor = imgui.ColorConvertFloat4ToU32(imgui.GetStyle().Colors[imgui.Col.WindowBg])
    imgui.PushStyleColor(imgui.Col.Border, imgui.ImVec4(0, 0, 0, 0))
    imgui.BeginChild(str_id, size, true)
    imgui.SetCursorPos(imgui.ImVec2(0, 30))
    imgui.Spacing()
    imgui.PopStyleColor(1)
    size.x = size.x == -1.0 and imgui.GetWindowWidth() or size.x
    size.y = size.y == -1.0 and imgui.GetWindowHeight() or size.y
    DL:AddRect(posS, imgui.ImVec2(posS.x + size.x, posS.y + size.y), imgui.ColorConvertFloat4ToU32(colorLine), rounding, _, 1)
    DL:AddRectFilled(imgui.ImVec2(posS.x, posS.y), imgui.ImVec2(posS.x + size.x, posS.y + size.x/size.y + 25), imgui.ColorConvertFloat4ToU32(colorBegin), rounding, 1 + 2)
    DL:AddText(imgui.ImVec2(posS.x + offset, posS.y + 15 - (sizeT.y / 2)), imgui.ColorConvertFloat4ToU32(colorText), title)
end

function buttonactivated()
for k, v in pairs(button_settings) do
  -- Код обработки кнопки
end

end

function keyactivate()
 for k, v in pairs(button_settings) do
 if button_settings[k].statusbutton.value then
    hotkeys[k] = rkeys.registerHotKey(button_settings[k].key, true,
      function()
          binderkeycontent = button_settings[k].text..'\n'
          for s in binderkeycontent:gmatch('[^\r\n]+') do
		  if not sampIsChatInputActive() then
            sampSendChat(s, 0xFFFFFF)
            wait(button_settings[k].delay)
          end
		  end
      end)
	 else -- иначе, если statusbutton установлен в false
if hotkeys[k] then -- если ранее клавиша была зарегистрирована
hotkeys[k] = rkeys.unRegisterHotKey(button_settings[k].key)
end
end
    end
end
function saveData(table, path)
if table == nil then return end
local file, message = io.open(path, "w+")
if not file then
sampAddChatMessage("Ошибка открытия файла: " .. message)
return
end
file:seek("set",0)
local success, message = file:write(json.encode(table))
if not success then
print("Ошибка записи файла: " .. message)
end
file:close()
end
function removeRow(table, row)
for i, v in ipairs(table) do
if v == row then
table.remove(table, i)
break
end
end
end
function getTime(timezone)
    local https = require 'ssl.https'
    local time = https.request('http://alat.specihost.com/unix-time/')
    return time and tonumber(time:match('^Current Unix Timestamp: <b>(%d+)</b>')) + (timezone or 0) * 60 * 60
end
function registerCommands()
    for k, v in pairs(command_setting) do
	 if command_setting[k].statuscommand.value then
        if sampIsChatCommandDefined(command_setting[k].command) then sampUnregisterChatCommand(command_setting[k].command) end
        sampRegisterChatCommand(command_setting[k].command, function(pam)
            lua_thread.create(function()
          			local cmdtext = command_setting[k].text
			                    for line in cmdtext:gmatch('[^\r\n]+') do
                            sampSendChat(line)
							wait(command_setting[k].delay)
                    end
        end)
		end)
		else
		  sampUnregisterChatCommand(command_setting[k].command)
    end
	end
end


function timeregister()
    for k,v in pairs(time_setting) do
	if time_setting[k].statustime.value then
                if time_setting[k].time == os.date("%X",os.time()) then
          			local cmdtext = time_setting[k].text
			                    for line in cmdtext:gmatch('[^\r\n]+') do
                            sampSendChat(line)
							time_setting[k].statustime.value = false
							wait(time_setting[k].delay)
													saveData(time_setting, "moonloader/logo/timelist.json")
                    end
				end
	end
	end
end

function fastmainmenu()
	    if not sampIsChatInputActive() then
	mainfastmenu = not mainfastmenu
	windowfastmenu()
	end
end

function sendChatWithDelay(text, delay)
lua_thread.create(function()
for line in text:gmatch('[^\r\n]+') do
sampSendChat(line)
wait(delay)
end
end)
end	
function linear_congruential_generator(a, c, m, X_prev)
local X_next = (a * X_prev + c) % m
local random_number = math.floor(X_next / 37)
return math.fmod(random_number, 37)
end


-- Регистрируем функцию renderBulletTracer для от


function samp.onSendBulletSync(data)
    if data.targetType == 0 then -- Цифра 1 отвечает за попадание по педам, то есть 'Если я попал по игроку, то'
          if data.center.x ~= 0 and data.center.y ~= 0 and data.center.z ~= 0 then
        -- Добавляем новые координаты в таблицу bulletPoints
        table.insert(bulletPoints, {
            start = {x = data.origin.x, y = data.origin.y, z = data.origin.z},
            ends = {x = data.target.x, y = data.target.y, z = data.target.z},
            color = {r = 15, g = 0, b = 0},
            time = os.time() + tonumber(BulletTime[0]) -- Время отображения трейсера в секундах
        })
    end
    end
	    if data.targetType == 1 then -- Цифра 1 отвечает за попадание по педам, то есть 'Если я попал по игроку, то'
                if data.center.x ~= 0 and data.center.y ~= 0 and data.center.z ~= 0 then
        -- Добавляем новые координаты в таблицу bulletPoints
        table.insert(bulletPoints, {
            start = {x = data.origin.x, y = data.origin.y, z = data.origin.z},
            ends = {x = data.target.x, y = data.target.y, z = data.target.z},
            color = {r = 15, g = 0, b = 0},
            time = os.time() + tonumber(BulletTime[0]) -- Время отображения трейсера в секундах
        })
    end
    end
	    if data.targetType == 2 then -- Цифра 1 отвечает за попадание по педам, то есть 'Если я попал по игроку, то'
               if data.center.x ~= 0 and data.center.y ~= 0 and data.center.z ~= 0 then
        -- Добавляем новые координаты в таблицу bulletPoints
        table.insert(bulletPoints, {
            start = {x = data.origin.x, y = data.origin.y, z = data.origin.z},
            ends = {x = data.target.x, y = data.target.y, z = data.target.z},
            color = {r = 15, g = 0, b = 0},
            time = os.time() + tonumber(BulletTime[0]) -- Время отображения трейсера в секундах
        })
    end
    end
    -- Проверяем, что координаты не равны 0, чтобы избежать лишней обработки
end

function renderBulletTracer()
-- Проходимся по всем точкам трейсера
for i, point in ipairs(bulletPoints) do
-- Если время отображения истекло, удаляем точку
if os.time() > point.time then
table.remove(bulletPoints, i)
else
renderDrawLineBy3dCoords(point.start.x, point.start.y, point.start.z, point.ends.x, point.ends.y, point.ends.z, tonumber(BulletWH[0]), 0xffff0000)
-- fireSingleBullet(point.start.x, point.start.y, point.ends.x, point.ends.y, point.color.r, point.color.g, point.color.b, 255)
end
end

end
function renderDrawLineBy3dCoords(posX, posY, posZ, posX2, posY2, posZ2, width, color)
    local SposX, SposY = convert3DCoordsToScreen(posX, posY, posZ)
    local SposX2, SposY2 = convert3DCoordsToScreen(posX2, posY2, posZ2)
    if isPointOnScreen(posX, posY, posZ, 1) and isPointOnScreen(posX2, posY2, posZ2, 1) then
        renderDrawLine(SposX, SposY, SposX2, SposY2, width, color)
		renderDrawPolygon(SposX2, SposY2, 5, 5, 5, 90.0, 0xffff0000)
    end
end
function samp.onSendGiveDamage(id, damage, weapon, bodypart)
	--sampAddChatMessage(damage)
end
function join_argb(a, r, g, b)
    local argb = b  -- b
    argb = bit.bor(argb, bit.lshift(g, 8))  -- g
    argb = bit.bor(argb, bit.lshift(r, 16)) -- r
    argb = bit.bor(argb, bit.lshift(a, 24)) -- a
    return argb
end
function sv()
	CheckBoxWorkOff.v = not CheckBoxWorkOff.v
	lockPlayerControl(CheckBoxWorkOff.v)
	if CheckBoxWorkOff then
		memory.write(0x747FB6, 0x1, 1, true)
		memory.write(0x74805A, 0x1, 1, true)
		memory.fill(0x74542B, 0x90, 8, true)
		memory.fill(0x53EA88, 0x90, 6, true)
		sampAddChatMessage('Работа в свёрнутом режиме включена', -1)
	else
		memory.write(0x747FB6, 0x0, 1, true)
		memory.write(0x74805A, 0x0, 1, true)
		arr = { 0x50, 0x51, 0xFF, 0x15, 0x00, 0x83, 0x85, 0x00 }
		memset(0x74542B)
		arr = { 0x0F, 0x84, 0x7B, 0x01, 0x00, 0x00 }
		memset(0x53EA88)
		sampAddChatMessage('Работа в свёрнутом режиме отключена', -1)
	end
end
function samp.onBulletSync(id, data)

	local hit_name = u8('ПРОМАХ')
	if data.targetType == 1 then hit_name = sampGetPlayerNickname(data.targetId) end
	if data.targetType == 2 then hit_name =  u8("Автомобиль").."["..data.targetId.."]" end
	------------------------
	local O, T = data.origin, data.target
local distance = 0
if O.z and T.z then
    distance = math.sqrt((O.x - T.x) ^ 2 + (O.y - T.y) ^ 2 + (O.z - T.z) ^ 2)
end
	local dist_text = ''
	if data.targetType == 1 or data.targetType == 2 then dist_text = ', ' .. math.floor(distance) .. 'm' end
-- sampAddChatMessage(id.." "..data.targetType.." "..data.weaponId..dist_text, 0xFFFFF)
    if data.targetType == 0 then -- мимо
    end
	    if data.targetType == 1 then -- Цифра 1 отвечает за попадание по педам, то есть 'Если я попал по игроку, то'
		--	sampAddChatMessage("Игрок: "..sampGetPlayerNickname(id).."["..id.."]".."["..sampGetPlayerHealth(id).."] попал в "..sampGetPlayerNickname(data.targetId).."["..data.targetId.."]["..sampGetPlayerHealth(data.targetId).."]", 0xFFFFFF)
    end
	    if data.targetType == 2 then -- машина
    end
	    table.insert(shoots_log, {name = sampGetPlayerNickname(id), nameid = id, hpname = sampGetPlayerHealth(id), types = data.targetType, dist = dist_text, namesh = hit_name})
end	 
function get_crosshair_position()
    local vec_out = ffi.new("float[3]")
    local tmp_vec = ffi.new("float[3]")
    ffi.cast(
        "void (__thiscall*)(void*, float, float, float, float, float*, float*)",
        0x514970
    )(
        ffi.cast("void*", 0xB6F028),
        15.0,
        tmp_vec[0], tmp_vec[1], tmp_vec[2],
        tmp_vec,
        vec_out
    )
    return vec_out[0], vec_out[1], vec_out[2]
end
function imgui.CenterColumnText(text)
    imgui.SetCursorPosX((imgui.GetColumnOffset() + (imgui.GetColumnWidth() / 2)) - imgui.CalcTextSize(text).x / 2)
    imgui.Text(text)
end
function radarAndHudPatch(bool)
    if bool then
        memory.write(sampGetBase() + 643864, 37008, 2, true)
    else
        memory.write(sampGetBase() + 643864, 3956, 2, true)
    end
end
function samp.onSetPlayerDrunk(drunkLevel)
    return {1}
end
