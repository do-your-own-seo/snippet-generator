<script>
    let [title,descr,extra,long]=['','','',''],
        titleSample = 'A neat title up to 600px (about 55-60 characters)',
        descrSample = 'Two-line description 600px wide. Depending on the location of the search phrase, about 140 - 180 chars to be displayed. If the date is specified, fewer description chars will be shown...',
        extraSample = 'Mobile users may see even more, up to four lines of the blue title',
        tooltip = 'Chars above 140 are more likely to be cut out for desktop users',
        regex = /^[A-Z|a-z|0-9|А-Я|Є|І|а-я|є|і|ё]$/,
        now = new Date(), dateNeeded=true,
        smallScreen = window.matchMedia("screen and (max-width: 639px)").matches,
        temp = document.createElement('textarea'), 
        ctx = document.createElement("canvas").getContext("2d");

    const decodeEntity = (str)=>{
            temp.innerHTML=str;
            return temp.value
        },
        emojiSupport = ()=>{
          ctx.fillText("🍕", -2, 4);
          return ctx.getImageData(0, 0, 1, 1).data[3] > 0; // Non-transparent pixel
        },
        substrNoBreak = (str,firstPos,lastPos,lookBack)=>{
            //Breaks the description in between words
            let substr = str.substring(firstPos,lastPos), 
                ellipsis = str.length > lastPos;
            if (lookBack && regex.test(str[firstPos-1]) && regex.test(str[firstPos])) {
                substr = str.substring(0,firstPos).split(' ').pop()+ substr;
            }
            if (str[lastPos] && regex.test(str[lastPos-1]) && regex.test(str[lastPos])){
                substr = substr.substring(0,substr.lastIndexOf(' '));
            }
            return ellipsis ? substr+(smallScreen ? ' ' : '...') : substr;
        }
    $: date = dateNeeded ? now.getDate()+' '+now.toLocaleString('default', { month: 'long' })+' '+now.getFullYear()+' - ' : '';
    $: snippet = date + descr; 
    $: long = (snippet.length >139)?substrNoBreak(snippet,140,180):''; 
    $: extra = (snippet.length >179)?substrNoBreak(snippet,180,280,true):'';
</script>
<h1 id="simulator"><span class="green">Google Snippet</span> &nbsp;Preview</h1>
<form>
    <div>
        <textarea id="title" bind:value={title} on:input="{e=>{title=decodeEntity(title)}}" placeholder=" Your title here" maxlength="99" cols="24" rows="3"></textarea>
        <label for="title">{title.length ? '= ' + title.length : '= 0'}</label>
    </div>
    <div>
        <textarea id="descr" bind:value={descr} on:input="{e=>{descr=decodeEntity(descr)}}" placeholder=" Your description here" maxlength="280" cols="46" rows="3"></textarea>
        <label for="descr">{descr.length ? '= '+ descr.length : '= 0'}</label>
        <div>
            <label><input type="checkbox" bind:checked={dateNeeded}>Display the date</label>
        </div>
    </div>
</form>

<div class="g">
    <cite>example.com › Bread › Crumbs</cite>
    <div class="rc">
        <div class="r">
            <span class="a">
                <h3>{title?title:titleSample}</h3>
            </span>
        </div>
        <div class="s">
            <span class="f">{date}</span><span class="st">{descr?snippet.substring(date.length,140):descrSample}<span class="long" title={tooltip}>{long}</span></span>
            {#if snippet.length > 180 || !descr} {#if !smallScreen}
                <br><i class="separator">________</i><br>{/if}<span class="st mobile">{extra?extra:extraSample}</span>
            {/if}
        </div>
    </div>
</div>
<div class="emoji">
    <p>You may want to copy-paste to input fields some Unicode characters</p> 
    <p class="unicode">➊ ➋ ➌ ➍ ➎ ➥ ➦ ✔ ✖ ⚑ ✆ ☎ ✈ ✉ ✎ § ⚖ (͡๏̯͡๏) 〠 ♛ ♜ ♝ ♞ ✮ ✰ ✿ ൡ</p>
    <p>or even colorful emoji from <a href="https://emojipedia.org/">emojipedia</a>.</p>

    {#if emojiSupport()}
    <p class="unicode">🍊 &thinsp; 🍾 &thinsp; 🍕 &thinsp; 🍏 &thinsp; 🌶️ &thinsp; 🍫 &thinsp; 🎡 &thinsp; 🧐 &thinsp; 🎠 &thinsp; 🛩️ &thinsp; 📗 &thinsp; ⏰ &thinsp; ⌛ &thinsp; 📙 &thinsp; 📈 &thinsp;🇨🇦 &thinsp; 🇺🇦 &thinsp; ⚽  &thinsp; 🌎</p>
    {/if}
    <p>But there is no guarantee that special characters will be displayed in your real snippet.</p>
</div>

<style>
:global(#component) {
    width: 680px;
    max-width: 100%;
    margin: 28px auto;
    font-family: arial, sans-serif;
    font-size: 14px;
    color: #222;
}

.g {
    width: 600px;
    max-width: 94%;
    margin: auto;
    line-height: 1.2;
}


.rc {
    position: relative;
    margin-bottom: 42px;
}

.rc h3 {
    display: inline-block;
    font-size: 20px;
    font-weight: normal;
    line-height: 24px;
    margin: 0 0 3px 0;
    padding: 12px 0;
}

h1 {
    padding: .5em 0 1em 0;
    text-align: center;
}

.r {
    margin: 0;
    font-size: small;
    
}

.emoji {
    text-align: center;
}

.r,.unicode {
    line-height: 1.58
}

.unicode {
    font-size: 18px;
}

.a {
    color: #1a0dab
}

.a:hover {
    text-decoration: underline
}

cite, label {
    font-style: normal;
    font-size: 14px;
    line-height: 1.3
}

cite {
    opacity: .7;
}

form {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
}

form > div {
    margin-bottom: 14px;
}

form div div{
    margin: 4px 30px 0 0;
    text-align: right;
}

textarea {
    overflow-y: hidden;
    max-width: calc(90vw - 30px);
    border-radius: 4px;
}

label{
    margin-right: 18px;
}

.s {
    max-height: 88px;
    overflow: hidden;
}

.s b {
    color: #52565A
}

.st {
    line-height: 1.57;
}

.f {
    color: #70757a
}

.separator {
    display: none;
}

.mobile {
    background: #d8ffb1;
}

@media screen and (min-width: 640px) {
    h3 {
        line-height: 1.3;
        max-height: 27px;
        max-width: 94%;
        padding: 4px 0 0 0;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
    }

    .s {
        max-height: none;
    }

    .long {
        background: #ffc057;
    }

    .separator {
        display: inline;
    }
}

</style>
