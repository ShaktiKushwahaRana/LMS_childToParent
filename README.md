# LMS_childToParent

CHILD TO PARENT COMMUNICATION USING LMS


------------------------------------------------
Message Channel--->Folder:messageChannels-->myChanneldemo.messageChannel-meta.xml
-----------------------------------------------------------------------------------

<?xml version="1.0" encoding="UTF-8" ?>
<LightningMessageChannel xmlns="http://soap.sforce.com/2006/04/metadata">
    <masterLabel>myChanneldemo</masterLabel>
    <isExposed>true</isExposed>
    <description>Message Channel to pass a entity Id</description>
    <lightningMessageFields>
        <fieldName>myText</fieldName>
        <description>To send data from One component to anaother</description>
    </lightningMessageFields>
</LightningMessageChannel>
-------------------------------------------------------------------------------
Child Component
---------------------------------------------
<template> 
  
               
            <lightning-card title="Publish To Parent" 
            icon-name="custom:custom33">

            <br>
            <div style="width:200px; padding-left:50px">
                <lightning-input class="inp" label="Enter Name" name="input3"></lightning-input>
                <lightning-input class="inp" label="Enter Age" name="input4"></lightning-input>
                <br>
               
                <lightning-button label="Click to Publish" onclick={handleClick} variant="brand"></lightning-button>
               
            </div>



        </lightning-card>
</template> 
------------------------------------------------------------------

import { LightningElement,track, wire } from 'lwc';
import messageChannel from '@salesforce/messageChannel/myChanneldemo__c';
import {publish, subscribe, MessageContext } from 'lightning/messageService';

export default class Childpp extends LightningElement {

    @track input1;
    @track input2;
    @track input3;
    @track input4;
    @track demoObject=[];
    @track myArr=[];
    @wire(MessageContext)
    messageContext;
    

    @wire(MessageContext)
    messageContext;
    handleClick(event)
    {
        console.log(event.target.label);
        var inp=this.template.querySelectorAll("lightning-input");
        inp.forEach(function(element){
            if(element.name=="input3")
                this.input3=element.value;

            else if(element.name=="input4")
                this.input4=element.value;
        },this);
        this.handleButtonClick();
    }

    handleButtonClick() {
        this.myArr[0]=this.input3;
        this.myArr[1]=this.input4;
        let message = {myText: this.myArr};
        publish(this.messageContext, messageChannel, message);
    }

}
----------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>52.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__AppPage</target>
        <target>lightning__RecordPage</target>
        <target>lightning__HomePage</target>
        <target>lightningCommunity__Page</target>
    </targets>
</LightningComponentBundle>
__________________________________________________________________________________
PARENT COMPONENT
------------------------------------------------------------------------
<template> 
    
    
            <lightning-card title="Subscription from child conponent" 
            icon-name="custom:custom33">
        <p>PARENT SUBSCRIBE TO CHILD</p>
            <br>
                    <div style="width:200px; padding-left:50px">
                        <lightning-input class="inp" label="Enter Name" name="input3" value={input3}></lightning-input>
                        <lightning-input class="inp" label="Enter Age" name="input4" value={input4}></lightning-input>
                        <br>
                       
                            <lightning-button label="Click to  Subscribe Child" onclick={handleSubscribe} variant="brand"></lightning-button>
                       
                    </div>
        <P>CHILD COMPONENT</P>
        <c-childpp></c-childpp>
        </lightning-card>
        
     
</template> 
-------------------------------------------------------------------------------------------------------------------
import { LightningElement,track,wire } from 'lwc';
import { publish,subscribe, MessageContext } from 'lightning/messageService';
import messageChannel  from '@salesforce/messageChannel/myChanneldemo__c';

export default class Childpp extends LightningElement {

    @track input1;
    @track input2;
    @track input3;
    @track input4;
    @track myObj=[];
    subscription = null;
    @wire(MessageContext)
    messageContext;
    connectedCallback() {
        this.handleSubscribe();
    }
   
    
    handleSubscribe() {
        
        if (this.subscription) {
            return;
        }
        this.subscription = subscribe(this.messageContext, messageChannel, (message) => {
            console.log(message.myText);
            console.log('Subscribed');
            this.input3=message.myText[0];
            this.input4=message.myText[1];

        });
    }
}
