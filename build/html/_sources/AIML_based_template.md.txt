基于AIML的技术文档开发模板
======================
在这一节笔者将分享如何把基于dita开发的技术文档转化为基于AIML的聊天机器人。使用的案例是 [HP Photosmart 420 Series](http://h10032.www1.hp.com/ctg/Manual/c00446517.pdf) 用户手册。

## 聊天机器人开发模板
上一节我们提到了在DITA中有四种基本主题类型：
* 任务主题（task topic）
* 概念主题（concept topic）
* 参引主题（reference topic）
* 故障解决为主题（troubleshooting topic）  

这也就意味着，我们的机器人要向用户提供以上四种类型的信息，因此，在一开始，机器人要询问用户，是要对哪一内容进行询问。这就用到了AIML中的 `button` 标签，button提供给用户四种类型的选择，用户点击其中一种，比如“Met A Problem”,那么系统就会转入“Met A Problem”的 `topic` 标签中，之后用户不管是点击机器人提供的选项还是自己输入，都会在那个topic里面进行。  

用户在点击“Met A Problem”之后，机器人会为用户提供两种输入选择，第一种是用户主动输入，第二种是通过点击机器人提供的选项输入。两种输入方式的回答都指向一个位置，前者使用的是 `srai` 标签，后者使用的是 `postback` 标签。

这其中还涉及到许多其他标签，比如 `<delay>` 可以设置返回信息的速度，`<formal>` 可以调整输出文字的格式等等。下面的动图展示了最终的效果：  

![1](images/1.gif)

这一效果的模板如下：  

```
	<category>
	    <pattern>HI</pattern>
	    <template>
	        Hi, I'm HP Desktop Printer Assistant. How can I help you?<split/><delay>2</delay>
	        Have you met a problem, do you want to know more about your printer or want to finish a task?
	        <button>
	            <text>
	                <formal>met a problem</formal>
	            </text>
	            <postback>
	                METAPROBLEM
	            </postback>
	        </button>
	        <button>
	            <text>
	                <formal>know more</formal>
	            </text>
	            <postback>
	                KNOWMORE
	            </postback>
	        </button>
	        <button>
	            <text>
	                <formal>finish a task</formal>
	            </text>
	            <postback>
	                FINISHATASK
	            </postback>
	        </button>
	    </template>
    </category>
    
    <category>
        <pattern>METAPROBLEM</pattern>
        <template>
            I'm sorry that you met a problem.<split/><delay>2</delay>
            Can you tell me the model number of your printer👂?
            <think><set name="topic">problem</set></think>
        </template>
    </category>
    
    <category>
        <pattern>FINISHATASK</pattern>
        <template>
            Can you tell me the model number of your printer👂?
            <think><set name="topic">task</set></think>
        </template>
    </category>
    
    <category>
        <pattern>KNOWMORE</pattern>
        <template>
            Glad to hear that you want to know more!<split/><delay>1</delay>
            May I know the model number of your printer?
            <think><set name="topic">knowmore</set></think>
        </template>
    </category>
    

<topic name="problem">
    <category>
        <pattern>*</pattern>
        <template>
            Got it! Your printer's number is <star/>. Don't worry, I'll help you out😁.<split/><delay>1</delay>
            Can you tell me what happened, sweetie?<split/><delay>2</delay>
            Here are some common problems.<break/>
            
            <button>
                <text>Why are the letters too small when printing documents or web pages?</text>
                <postback>Q2</postback>
            </button>
            <button>
                <text>The Status light is flashing red</text>
                <postback>Q3</postback>
            </button>
        </template>
    </category>

    <category>
        <pattern>^ letters ^ too small ^</pattern>
        <template><srai>Q2</srai></template>
    </category>

    <category>
        <pattern>Q2</pattern>
        <template>
            If printouts are too small or the letters are hard to read, change the font size settings in the application or web browser you are printing from to resolve the issue.<split/><delay>2</delay>
            Do you need other help?
            <button>
                <text>I met another problem</text>
                <postback></postback>
            </button>
            <button>
                <text><formal>get back to the start</formal></text>
                <postback>HI</postback>
            </button>
        </template>
    </category>
    
    <category>
        <pattern>Q2.1</pattern>
        <template>
            Use the font size menu to increase the font size in Microsoft word processing apps.
            <ol>
                <li>Open the document, then press the Ctrl + A keys to select all the text in the document</li>
                <li>Select a larger font size from the font settings menus.</li>
            </ol>
        </template>
    </category>
    
    <category>
        <pattern>Q3</pattern>
        <template>
            The Status light is flashing red because an error is occured. <split/><delay>2</delay>
            You can do the following things to get it right.<split/><delay>1</delay>
            <ol>
                <li>Check the camera Image Display for instructions. If you have a digital camera connected to the printer or the bundled camera in the camera dock, check the camera screen for instructions. If the printer is connected to a computer, check the computer monitor for instructions.</li>
                <li>Turn off the printer.</li>
                <li>If the Status light continues to flash, go to    
                
                <link>
                    <text>HP官网</text>
                    <url>www.hp.com</url>
                </link> or contact HP Customer Car</li>
            </ol><delay>2</delay>
            Do you need other help?
            <button>
                <text>I met another problem</text>
                <postback></postback>
            </button>
            <button>
                <text><formal>get back to the start</formal></text>
                <postback>HI</postback>
            </button>
        </template>
    </category>
</topic>

<topic name="task">
    <category>
        <pattern>*</pattern>
        <template>
            Got it! Your printer's number is <star/>. <split/><delay>1</delay>
            You can input what you want to do or click the options below.
            <button>
                <text>Getting Ready to Print</text>
                <postback>getready</postback>
            </button>
            <button>
                <text>How do I save print settings as a new shortcut or as the defaults for future print jobs?</text>
                <postback>Q1</postback>
            </button>
            <button>
                <text>How do I print documents from my phone or tablet?</text>
                <postback>Q4</postback>
            </button>
            
            <button>
                <text>Maintaining and transporting the printer</text>
                <postback>mandt</postback>
            </button>
        </template>
    </category>
    
    <category>
        <pattern>Q1</pattern>
        <template>
            Create a new custom shortcut in the Document Properties or set print job defaults in the HP driver.<split/>
            To create a custom shortcut (if the Printing Shortcut tab is available), click one of the print job shortcuts, change any of the settings, click User Specified Print Settings, then click Save as.<split/>
            To set default settings for all print jobs, continue with these steps.<split/>
            <ol>
                <li>Search Windows for 'printers', then click Devices and Printers in the search results.</li>
                <li>Right-click the icon for your printer, then click Printer Properties</li>
                <li>Click the Advanced tab, then click Printing Defaults.</li>
                <li>Change any settings you want as defaults, then click OK.</li>
            </ol>
        </template>
    </category>
    
    <category>
        <pattern>Q4</pattern>
        <template>Most HP printers connected to your local wireless network support printing from a mobile device that is connected to the same network. Go to one of the following documents for steps and requirements to print with HP apps or built-in print features.</template>
    </category>
    
    <category>
        <pattern>getready</pattern>
        <template>
            Before you can begin printing, there are some easy procedures you need to become familiar with:<break/>
            <ol>
                <li>Loading paper</li>
                <li>Inserting print cartridges</li>
                <li>Connecting the camera</li>
            </ol>
        </template>
    </category>
    
    <category>
        <pattern>mandt</pattern>
        <template>
            The HP Photosmart 420 series GoGo Photo Studio requires very little maintenance. Follow the guidelines in this chapter to extend the life span of the printer and printing supplies, and to ensure that your prints are always of the highest quality.<split/><delay>2</delay>
            Do you need other help?
            <button>
                <text>I met another problem</text>
                <postback></postback>
            </button>
            <button>
                <text><formal>get back to the start</formal></text>
                <postback>HI</postback>
            </button>
        </template>
    </category>
</topic>
    
<topic name="knowmore">
    <category>
        <pattern>*</pattern>
        <template>
            Got it. Your printer's number is <star/>.<split/>
            What do you want to know about your printer?<break/>
            
            <button>
                <text>Printing Basics</text>
                <postback>printingbasics</postback>
            </button>
            <button>
                <text>System Requirements</text>
                <postback>SR</postback>
            </button>
        </template>
    </category>
        
    <category>
        <pattern>printingbasics</pattern>
        <template>
            The HP Photosmart 420 series GoGo Photo Studio lets you produce amazing prints without even going near a computer. After setting up the printer using the setup instructions that came in the box, you are just a few steps away from printing your images. This section describes:<split/><delay>2</delay>
            <ol>
                <li>Printing from the Docked Camera</li>
                <li>Printing from Other Devices</li>
            </ol><split/><delay>1</delay>
            Note In the following instructions, always use the buttons on the printer control panel, unless instructed otherwise. Also note that most camera buttons are disabled when the camera is in the camera dock. The only exception is the camera ON/OFF switch which turns off only the camera.<split/><delay>2</delay>
            Do you need other help?
            <button>
                <text><formal>I want to know more</formal></text>
                <postback></postback>
            </button>
            <button>
                <text><formal>get back to the start</formal></text>
                <postback>HI</postback>
            </button>
        </template>
    </category>
</topic>
```




