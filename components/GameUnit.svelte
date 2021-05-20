<script>
    export let index;
    let getStyles = function() {
        let styles = "";
        if(index[1] === 0) {
            styles += "border-left: 0px;";
        }
        if(index[1] === 2) {
            styles += "border-right: 0px;";
        }
        if(index[0] === 0) {
            styles += "border-top: 0px;";
        }
        if(index[0] === 2) {
            styles += "border-bottom: 0px;";
        }
        return styles;
    }
</script>
<style>
    .unit {
        border: 5px solid white;
        width: 32%;
        height: 32%;
        padding: 0px;
        color: white;
        text-align: center;
        font-weight: bolder;
        font-size: 90px;
        font-family: Georgia, 'Times New Roman', Times, serif;
        line-height: 150px;
    }
</style>

<div class="unit" style={getStyles()} i={index[0]} j={index[1]}>

</div>